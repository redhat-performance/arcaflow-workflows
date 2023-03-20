# Cluster-density Workflow

## Workflow Description

This example workflow runs a [web-burner](https://github.com/redhat-performance/web-burner) cluster-density workload plugin on the local baremetal cluster.

## Workflow Diagram
This diagram shows the complete end-to-end workflow logic.

```mermaid
flowchart TD
subgraph input
input.kubeconfig_str
input.wb_bfd_enabled
input.wb_number_of_nodes
input.wb_indexing
input.wb_es_index
input.wb_scale_factor
input.wb_es_server
end
input.wb_es_server-->steps.web_burner
input.kubeconfig_str-->steps.web_burner
input.kubeconfig_str-->steps.delete_web_burner
input.wb_bfd_enabled-->steps.web_burner
input.wb_bfd_enabled-->steps.delete_web_burner
steps.metadata.outputs.success-->output
steps.delete_web_burner.outputs.success-->output
input.wb_number_of_nodes-->steps.web_burner
steps.metadata-->steps.metadata.outputs.success
steps.metadata-->steps.metadata.outputs.error
steps.uuidgen-->steps.uuidgen.outputs.success
steps.uuidgen-->steps.uuidgen.outputs.error
steps.uuidgen.outputs.success-->steps.web_burner
steps.uuidgen.outputs.success-->steps.delete_web_burner
steps.uuidgen.outputs.success-->output
steps.web_burner-->steps.web_burner.outputs.error
steps.web_burner-->steps.web_burner.outputs.success
input.wb_indexing-->steps.web_burner
input.wb_es_index-->steps.web_burner
input.wb_scale_factor-->steps.web_burner
input.wb_scale_factor-->steps.delete_web_burner
steps.web_burner.outputs.success-->output
steps.delete_web_burner-->steps.delete_web_burner.outputs.error
steps.delete_web_burner-->steps.delete_web_burner.outputs.success
```