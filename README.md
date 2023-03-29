# Arcaflow Workflows

## About Arcaflow
Arcaflow is a workflow engine that uses plugins to run a graph of container-based actions or workloads. Arcaflow requires no installation; only the `arcaflow` engine binary and a workflow file are needed to run a workflow. A workflow is typically initiated from a local system that has network access to remote Kubernetes or OpenShift cluster. A valid `kubeconfig` file is needed in order to run plugins in a remote cluster. Please see the [community documentation](https://arcalot.io/arcaflow) for more information.