# Node-density Workflow

***NOTE: This is an example only and may not work out-of-the-box.***

## Workflow Description

This example workflow runs a [kube-burner](https://github.com/cloud-bulldozer/kube-burner) node-density workload plugin on the local system.


## Files

- [`workflow.yaml`](workflow.yaml) -- Defines the workflow input schema, the plugins to run
  and their data relationships, and the output to present to the user
- [`input.yaml`](input-example.yaml) -- The input parameters that the user provides for running
  the workflow
- [`config.yaml`](config.yaml) -- Global config parameters that are passed to the Arcaflow
  engine
                     
## Running the Workflow

You will need a Golang runtime and Docker to run the containers (Podman can
be used with the [system service](https://docs.podman.io/en/latest/markdown/podman-system-service.1.html)
enabled for socket connections, which are required by the Arcaflow engine to
communicate with the plugins).

Clone the engine:
```
$ git clone https://github.com/arcalot/arcaflow-engine.git
```

Clone this workflows repo, and set this directory to your workflow working directory (adjust as needed):
```
$ git clone https://github.com/redhat-performance/arcaflow-workflows.git
  Modify arcaflow-workflows/kube-burner/node-density/input-example.yaml with the kubeconfig string of your openshift cluster
$ export WFPATH=$(pwd)/arcaflow-workflows/kube-burner/node-density
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
input.kubeburner_loglevel-->steps.kubeburner
input.kubeburner_es_index-->steps.kubeburner
input.kubeburner_burst-->steps.kubeburner
input.kubeburner_pod_ready_threshold-->steps.kubeburner
input.kubeburner_qps-->steps.kubeburner
input.kubeconfig_str-->steps.kubeburner
input.kubeburner_es_server-->steps.kubeburner
input.kubeburner_timeout-->steps.kubeburner
steps.uuidgen.outputs.success-->steps.kubeburner
steps.uuidgen.outputs.success-->output
steps.kubeburner.outputs.success-->output
steps.metadata-->steps.metadata.outputs.success
steps.metadata-->steps.metadata.outputs.error
input.kubeburner_pods_per_node-->steps.kubeburner
steps.uuidgen-->steps.uuidgen.outputs.success
steps.uuidgen-->steps.uuidgen.outputs.error
steps.kubeburner-->steps.kubeburner.outputs.error
steps.kubeburner-->steps.kubeburner.outputs.success
steps.metadata.outputs.success-->output
```