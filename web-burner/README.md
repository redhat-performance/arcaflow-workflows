# Web-burner Workflows

This folder contains example workflows for different [web-burner](https://github.com/redhat-performance/web-burner) workloads.

Each folder contains the below set of files which are necessary to run the workflows. 


## Files

- [`workflow.yaml`](workflow.yaml) -- Defines the workflow input schema, the plugins to run
  and their data relationships, and the output to present to the user
- [`input.yaml`](input-example.yaml) -- The input parameters that the user provides for running
  the workflow
- [`config.yaml`](config.yaml) -- Global config parameters that are passed to the Arcaflow
  engine


## Running the Workflow

Download the [Arcaflow engine binary](https://github.com/arcalot/arcaflow-engine/releases) to your local system or jump host with network access to your Kubernetes/OpenShift cluster.

wget https://github.com/arcalot/arcaflow-engine/releases/download/v0.3.2/arcaflow_0.3.2_linux_amd64.tar.gz
tar -xzf arcaflow_0.3.0_linux_amd64.tar.gz -C . arcaflow 

Clone this workflows repo, and set this directory to your workflow working directory (adjust as needed):
```
$ git clone https://github.com/redhat-performance/arcaflow-workflows.git
```
- Modify the [input-example.yaml](input-example.yaml) file to your needs.
  - *Note: This currently requires the complete kubeconfig to be in the input file as YAML string. We will be enabling direct file input in a future enhancement. Example multi-line YAML string syntax with indentation:*
    ```yaml
    kubeconfig: |
      apiVersion: v1
      clusters:
      - cluster:
          certificate-authority-data: 
          ...
    ```



Run the workflow (this example assumes the `arcaflow` binary is in your `$PATH` and the `workflow.yaml` file is in the local directory):
Run the workflow:

```bash
  $ arcaflow --input input.yaml --config config-podman.yaml
```


