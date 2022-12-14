# Automotive Sysbench + PCP Workflow

***NOTE: This workflow is a work-in-progress***

## Workflow Description

This workflow runs a [sysbench](https://github.com/akopytov/sysbench) CPU workload plugin on the local system.

In addition to the sysbench workload, the workflow collects system metrics with [Performance Co-pilot](https://pcp.io/), and collects system metadata using Ansible [gather facts](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/gather_facts_module.html). Finally the outputs are all collected into a document and indexed to an [OpenSearch](https://opensearch.org/)-compatible service such as [Elasticsearch](https://www.elastic.co/).

## Files

- [`workflow.yaml`](workflow.yaml) -- Defines the workflow input schema, the plugins to run
  and their data relationships, and the output to present to the user
- [`input.yaml`](input-example.yaml) -- The input parameters that the user provides for running
  the workflow
- [`config.yaml`](config.yaml) -- Global config parameters that are passed to the Arcaflow
  engine
                     
## Running the Workflow

### Starting an Elasticsearch Server for Testing

For workflow testing purposes, a [docker-compose-dev.yaml](docker-compose-dev.yaml)
file is included here that will start an Elasticsearch server pod locally. The
provided [input-example.yaml](input-example.yaml) file is set to use the default
Docker network IP for the host, and the server has authentication disabled, so in most cases this should work out-of-the box.

To start the Elasticsearch pod in the background:
```
$ docker-compose -f docker-compose-dev.yaml up -d
```

To stop the Elasticsearch pod:
```
$ docker-compose -f docker-compose-dev.yaml down -v
```

### Workflow Execution

You will need a Golang runtime and Docker to run the containers (Podman can
be used with the [system service](https://docs.podman.io/en/latest/markdown/podman-system-service.1.html)
enabled for socket connections, which are required by the Arcaflow engine to
communicate with the plugins).

Clone the engine:
```
$ git clone git@github.com:arcalot/arcaflow-engine.git
```

Clone this workflows repo, and set this directory to your workflow working directory (adjust as needed):
```
$ git clone https://github.com/arcalot/arcaflow-workflows.git
$ export WFPATH=$(pwd)/arcaflow-workflows/example-workflow
```
 
Run the workflow:
```
$ cd arcaflow-engine
$ go run cmd/arcaflow/main.go -input ${WFPATH}/input-example.yaml \
-config ${WFPATH}/config.yaml -context ${WFPATH}
```

## Workflow Diagram
This diagram shows the complete end-to-end workflow logic.

```mermaid
flowchart LR
subgraph input
input.user
input.cluster_name
input.run_id
subgraph sysbench
input.sysbench_threads
input.sysbench_events
input.sysbench_runtime
input.sysbench_cpumaxprime
end
subgraph elastic
input.elastic_password
input.elastic_index_sysbench
input.elastic_index_pcp 
input.elastic_username
input.elastic_host
end
subgraph pcp
input.pmlogger_interval
end
end
steps.system_config-->steps.system_config.outputs.error
steps.system_config-->steps.system_config.outputs.success
steps.elasticsearch_sysbench.outputs.success-->output
input.cluster_name-->output
input.cluster_name-->steps.elasticsearch_sysbench
input.cluster_name-->steps.elasticsearch_pcp
steps.pcp-->steps.pcp.outputs.success
steps.pcp-->steps.pcp.outputs.error
input.user-->steps.elasticsearch_sysbench
input.user-->steps.elasticsearch_pcp
input.user-->output
steps.elasticsearch_pcp.outputs.success-->output
steps.system_config.outputs.success-->steps.elasticsearch_sysbench
steps.system_config.outputs.success-->steps.elasticsearch_pcp
steps.system_config.outputs.success-->output
input.sysbench_cpumaxprime-->steps.sysbench
input.elastic_index_pcp-->steps.elasticsearch_pcp
steps.sysbench-->steps.sysbench.outputs.error
steps.sysbench-->steps.sysbench.outputs.success
steps.uuidgen.outputs.success-->steps.elasticsearch_sysbench
steps.uuidgen.outputs.success-->steps.elasticsearch_pcp
steps.uuidgen.outputs.success-->output
input.sysbench_runtime-->steps.pcp
input.sysbench_runtime-->steps.sysbench
steps.pcp.outputs.success-->steps.elasticsearch_pcp
steps.pcp.outputs.success-->output
steps.elasticsearch_sysbench-->steps.elasticsearch_sysbench.outputs.error
steps.elasticsearch_sysbench-->steps.elasticsearch_sysbench.outputs.success
input.sysbench_threads-->steps.sysbench
input.elastic_password-->steps.elasticsearch_sysbench
input.elastic_password-->steps.elasticsearch_pcp
input.sysbench_events-->steps.sysbench
input.elastic_host-->steps.elasticsearch_sysbench
input.elastic_host-->steps.elasticsearch_pcp
steps.uuidgen-->steps.uuidgen.outputs.success
steps.uuidgen-->steps.uuidgen.outputs.error
input.elastic_username-->steps.elasticsearch_pcp
input.elastic_username-->steps.elasticsearch_sysbench
input.run_id-->steps.elasticsearch_sysbench
input.run_id-->steps.elasticsearch_pcp
input.run_id-->output
input.pmlogger_interval-->steps.pcp
steps.sysbench.outputs.success-->steps.elasticsearch_sysbench
steps.sysbench.outputs.success-->output
steps.elasticsearch_pcp-->steps.elasticsearch_pcp.outputs.success
steps.elasticsearch_pcp-->steps.elasticsearch_pcp.outputs.error
input.elastic_index_sysbench-->steps.elasticsearch_sysbench
```
