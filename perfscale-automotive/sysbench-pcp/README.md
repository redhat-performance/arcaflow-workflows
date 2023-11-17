# Automotive Sysbench + PCP Workflow

***NOTE: This workflow is a work-in-progress***

## Workflow Description

This workflow runs a series of [sysbench](https://github.com/akopytov/sysbench) CPU workload plugins on the local system.

In addition to the sysbench workload, the workflow collects system metrics with [Performance Co-pilot](https://pcp.io/), and collects system metadata using Ansible [gather facts](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/gather_facts_module.html).

## Files

- [`workflow.yaml`](workflow.yaml) -- Defines the outer workflow input schema, the plugins and sub-workflow to run and their data relationships, and the output to present to the user
- [`sysbench-cpu.yaml`](sysbench-cpu.yaml) -- Defines the inner workflow input schema, plugins, and output. This workflow is looped over by the `workflow.yaml`, but it can also be used stand-alone.
- [`input.yaml`](input-example.yaml) -- Example input parameters that the user provides for running
  the outer workflow
- [`input-sysbench-cpu.yaml`](input-sysbench-cpu.yaml) -- Example input parameters that can be used with the inner `sysbench-cpu.yaml` workflow stand-alone.
- [`config.yaml`](config.yaml) -- Global config parameters that are passed to the Arcaflow
  engine
                     
## Running the Workflow

### Workflow Execution

You will need the Arcaflow engine binary and Podman or Docker to run the containers.

Download the appropriate engine binary:
https://github.com/arcalot/arcaflow-engine/releases

Clone this workflows repo, and set this directory to your workflow working directory (adjust as needed):
```
$ git clone https://github.com/arcalot/arcaflow-workflows.git
$ export WFPATH=$(pwd)/arcaflow-workflows/perfscale-automotive/sysbench-pcp
```
 
Run the workflow (by default this will use the `workflow.yaml` file in the context directory):
```
$ arcaflow -input ${WFPATH}/input.yaml -config ${WFPATH}/config.yaml -context ${WFPATH}
```

## Workflow Diagram
This diagram shows the complete end-to-end workflow logic.

TODO
