# Importing an existing cluster with the console

 After you install Red Hat Advanced Cluster Management for Kubernetes, you are ready to import a cluster to manage. You can import from both the console and the CLI. Follow this procedure to import from the console. You need your terminal for authentication during this procedure.

- [Prerequisites](#gui_prerequisites)
- [Importing a cluster](#importing-a-cluster)
- [Removing a cluster](#removing-an-imported-cluster)

## Prerequisites {#gui_prerequisites}

* You need a Red Hat Advanced Cluster Management for Kubernetes hub cluster deployed.
* You need a cluster that you want to manage and Internet connectivity.
* Install `kubectl`. To install `kubectl`, see _Install and Set Up kubectl_ in the [Kubernetes documentation](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-on-macos).

<!--Optionally, the Visual Web Terminal supports both `kubectl` and `oc` commands and can target alternative `contexts` to work with remote clusters. (wait for testing, elder sending RFE, see Brandi or elder for history here)-->

* You need the `base64` command line tool.

**Required user type or access level**: Cluster administrator

## Importing a cluster

You can import existing clusters from the Red Hat Advanced Cluster Management for Kubernetes console for each of the available cloud providers.

 **Note:** A hub cluster cannot manage _any_ other hub cluster; you must import an existing cluster.

1. From the navigation menu, hover over **Automate infrastructure** and click **Clusters**.

2. Click **Add cluster**.

3. Click **Import an existing cluster**.

4. Provide a cluster name. By default, the namespace is set to the same value as your cluster name. **Best practice:** Leave the namespace value and do not edit.

5. **Optional:** Click to expand **Edit cluster import YAML file** and modify the endpoint configuration.

   See [Table 1. YAML file parameters and descriptions](#yaml-parameters-and-descriptions) for details about each parameter.

6. **Optional**: After you import, you can add labels by clicking **Configure advanced parameters** and use these labels to search.

7. Click **Generate Command** to retrieve the command to deploy the `multicluster-endpoint`.

8. From the _Import an existing cluster_ window, hover and click the **Copy command** icon to copy the import command and the token that you are provided. You must click the **Copy** icon to receive the accurate copy.

9. From your terminal, authenticate to your managed cluster. Configure your `kubectl` for your targeted managed cluster.

  See [Supported cloud providers](../install/supported_clouds.md) to learn how to configure your `kubectl`.

10. To deploy the `multicluster-endpoint` to the managed cluster, run the command that you generated and copied from _step 8_.

11. Click **View cluster** to view the _Overview_ page and a summary of your cluster.

**Note** You can continue to import more clusters. Click **Import another** to repeat the process.

### YAML Parameters and descriptions

Table 1: The following table lists the parameters and descriptions that are available in the YAML file:

| Parameter | Description | Default value|
|---|---|---|
| clusterLabels| Provide cluster labels; you can add labels to your file | none |
| clusterLabels.cloud| The provider label for your cluster| auto-detect|
| clusterLabels.vendor| The Kubernetes vendor label for your cluster| auto-detect|
| clusterLabels.environment| The environment label for your cluster| none|
| clusterLabels.region| The region where your cluster is set up|none|
| applicationManager.enabled| Enables multicluster manager application deployment, deploys subscription controller and deployable controller | true |
| searchCollector.enabled| Enables search collection and indexing | true|
| policyController.enabled| Enable the Governance and risk dashboard policy feature|true, updateInterval: 15|  
| certPolicyController.enabled|Monitors certificate expiration based on distributed policies|true|
| cisController.enabled|Technology preview only; Monitors CIS security controls based on distributed policies|false|
| iamPolicyController|Monitors identity controls based on distributed policies|true|
| serviceRegistry.enabled| Service registry that is used to discover services that are deployed by Application Deployable among managed clusters.| false |
| serviceRegistry.dnsSuffix| The suffix of the registry DNS name, which is added to the end of the target clusters dns domain name.|mcm.svc|
| serviceRegistry.plugins| Comma-separated list of enabled plugins. Supported plugins: `kube-service`, `kube-ingress`, and `istio`. |kube-service|
| version| Version of `multicluster-endpoint` |1.0.0|
{: caption="Table 1. Table of YAML file parameters and descriptions for imported clusters" caption-side="}

## Removing an imported cluster

Complete the following procedure to remove an imported cluster and the `multicluster-endpoint` that was created on the managed cluster.

1. From the _Clusters_ page, find your imported cluster in the table.

2. Click **Options** > **Detach cluster** to remove your cluster from management.
