# Managing CIS policies

Apply a CIS policy to monitor the nodes in a cluster for compliance against CIS Kubernetes benchmark checks. Learn to create, apply, view, and update your CIS policies in the following sections.

## Creating a CIS policy 

You can create a YAML file for your CIS policy from the command line interface (CLI) or from the console. View the following sections to create a certificate policy:

### Creating a CIS policy from the CLI

Complete the following steps to create a certificate policy from the command line interface (CLI):

1. Create a YAML file for your CIS policy by including a set of exclude rules for master node and worker node. Run the following command:

  ```
  kubectl create -f CisPolicy.yaml
  ```
  
  Your YAML might resemble the following file:
  
  ```yaml	
  apiVersion: policies.ibm.com/v1alpha1	
  kind: CisPolicy	
  metadata:	
    labels:	
      controller-tools.k8s.io: "1.0"	
      pci-category: "system-integrity"	
    name: cispolicy-pci-sample	
  spec:	
    cisSpecVersion: 1.4	
    severity: medium	
    kubernetesCisPolicy:	
      masterNodeExcludeRules:	
      1.1 Maintain default behavior for anonymous access	
      1.2 Verify that the basic-auth-file method is not enabled	
      1.3 Insecure Tokens	
      1.4 Secure communications between the API server and master nodes	
      1.5 Prevent insecure bindings	
      1.6 Prevent insecure port access	
      1.7 Use Secure Ports for API Server Traffic	
      1.8 Do not expose API server profiling data	
      1.9 Verify repair-malformed-updates argument for API compatibility	
      1.10 Verify that the AlwaysAdmit admission controller is disabled	
      1.11 Manage the AlwaysPullImages admission controller	
      1.12 Use Security Context Constraints instead of DenyEscalatingExec admission	
      1.13 Use Security Context Constraints instead of the SecurityContextDeny admission controller	
      1.14 Manage the NamespaceLifecycle admission controller	
      1.15 Configure API server auditing - audit log file path	
      1.16 Configure API server auditing - audit log retention	
      1.17 Configure API server auditing - audit log backup retention	
      1.18 Configure audit log file size
      ....
      workerNodeExcludeRules:
      7.1 Use Security Context Constraints to manage privileged containers as needed
      7.2 Ensure anonymous-auth is not disabled
      7.3 Verify that the --authorization-mode argument is set to WebHook
      7.4 Verify the OpenShift default for the client-ca-file argument
      7.5 Verify the OpenShift default setting for the read-only-port argument
      7.6 Adjust the streaming-connection-idle-timeout argument
      7.7 Verify the OpenShift defaults for the protect-kernel-defaults argument
      7.8 Verify the OpenShift default value of true for the make-iptables-util-chains argument
      7.9 Verify that the --keep-terminated-pod-volumes argument is set to false
      7.10 Verify the OpenShift defaults for the hostname-override argument
      7.11 Set the --event-qps argument to 0
      7.12 Verify the OpenShift cert-dir flag for HTTPS traffic
      7.13 Verify the OpenShift default of 0 for the cadvisor-port argument
      7.14 Verify that the RotateKubeletClientCertificate argument is set to true
      7.15 Verify that the RotateKubeletServerCertificate argument is set to true
      8.1 Verify the OpenShift default permissions for the kubelet.conf file
      8.2 Verify the kubeconfig file ownership of root:root
      8.3 Verify the kubelet service file permissions of 644
      8.4 Verify the kubelet service file ownership of root:root
      8.5 Verify the OpenShift default permissions for the proxy kubeconfig file
      8.6 Verify the proxy kubeconfig file ownership of root:root
      8.7 Verify the OpenShift default permissions for the certificate authorities file.
      8.8 Verify the client certificate authorities file ownership of root:root
      
  ```
  
2. Apply the policy by running the following command:

   ```
   kubectl apply -f <cis-policy-file> --namespace=<namespace>
   ```

3. Verify and list the policies by running the following command:

   ```
   kubectl get cispolicy --namespace=<namespace>
   ```

Your CIS policy is created. 

#### Viewing a CIS policy from command line interface (CLI)

Complete the following steps to view the CIS policy from the managed cluster CLI:

1. Get a list of the CIS policies that are on your hub cluster by running the following command:

   ```
   oc get cispolicy --all-namespaces
   ```

2. Run the following command to view which policies are listed `NonCompliant`:

   ```
   oc describe cispolicy -n <namespace> <policyname>
   ```

   Your output might resemble the following response:

     ```
     Status:
       Cis Policy Status:
         Endpoint 4:
           Cluster Name:  endpoint4
           Compliancy:    NonCompliant
           Node Status:
             150 . 238 . 253 . 105:  NonCompliant
             150 . 238 . 253 . 106:  NonCompliant
             150 . 238 . 253 . 121:  NonCompliant
           Risk Score:
             Highest Risk Score:  5
             Risk Category:       medium
        Observed Generation:       2
     Events:
       Type     Reason       Age                From            Message
       ----     ------       ----               ----            -------
       Warning  Medium Risk  12s (x2 over 26s)  cis-controller  Medium Risk detected!
     ```

3. Verify which nodes are non-compliant by running the following commands:

   ```
   oc get nodes -o wide

   oc describe node <node-name>
   ```

   **Note**: The node name that appear might be a label for the node, for instance `ibm-cloud.kubernetes.io/external-ip=150.238.253.106`.

4. View the risk scores for the CIS rules in your policy by running the following command:

  ```
  kubectl -n <namespace> describe configmap endpoint-cisctrl-controller-config
  ```

### Creating a CIS policy from the console

As you create your CIS policy from the console, a YAML file is also created in the YAML editor. Complete the following steps to create CIS policy from the console:

1. Log in to the Red Hat Advanced Cluster Management for Kubernetes console.
2. From the navigation menu, click **Govern risk**.
3. Click **Create Policy**.
4. Enter the name for the CIS policy in the **Name** field.
5. For **Specifications**, select `Cispolicy-cis compliance for OCP` from the drop-down list.

   **Note**: You must enable the feature flag to make the `Cispolicy-cis compliance for OCP` option available.

6. Use the drop-down list and make selections for the following parameters:
    - Cluster selector
    - Standards
    - Categories
    - Controls
7. Click **Create**.

A policy is created and is embedded into the policy. 

#### Viewing a CIS policy from the console

View any CIS Policy and its status from the console.
1. Log in to the Red Hat Advanced Cluster Management for Kubernetes console.
2. From the navigation menu, click **Govern risk** to view a table list of your policies.

  **Note:** You can filter the table list of your policies by selecting the _All policies_ tab or _Cluster violations_ tab.

3. Select one of your policies.

4. View the policy violations by selecting the _Violations_ tab.

## Updating CIS policies

### Enabling the CIS policy controller from the CLI

The CIS policy controller monitors the nodes in a cluster for compliance against CIS Kubernetes benchmark checks. The CIS policies that list the rules to exclude can be applied to the managed clusters. The controller checks the cluster for any violations that are not in the exclude list.

When you install the Klusterlet, the CIS policy controller is disabled by default. Enable the controller after your cluster is imported by running the following command:

  ```
  kubectl patch endpointconfig $CLUSTER_NAME -n $CLUSTER_NAMESPACE --type='json' -p='[{"op": "replace", "path": "/spec/cisController/enabled", "value":true}]'
  ```
  
### Enabling the CIS policy controller from the console

Complete the following steps to enable the CIS policy controller feature flag from the console. The CIS policy specification becomes available:

1. Log in to your hub cluster and open the Visual Web terminal.

2. Edit the deployment to enable the feature flag. Run the following command:

   ```
   oc edit deployment $(oc get deployment -o custom-columns=:.metadata.name | grep 'grcui$')
   ```

3. Update the `env.value` parameter to `"yes"`. Your deployment might resemble the following content:

   ```
   -env
     - name: featureFlags_cisPolicyTemplate
       value: "yes"
   ```

4. Save your deployment and close the Visual Web Terminal.

5. The associated pod restarts. Attempt to create the policy again.

## Remediating CIS policy violation

Verify rule failures to remediate your CIS policy violations. Complete the following steps:

1. Run the following command to find the external IP address and node port for the CIS Minio component:
   
   ```
   oc get service endpoint-cisctrl-minio -n multicluster-endpoint
   ```

   The output might be similar to the following content:

   ```
   NAME                     TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)          AGE
   endpoint-cisctrl-minio   LoadBalancer   172.21.30.248   150.238.30.234   9000:31956/TCP   4h
   ```

   Open a browser and enter the external IP address and node port value. For example, your URL might be similar to following address: `https://150.238.30.234:31956`
   
2. Log in to your MinIO account with your access key and secret key, which is contained on the endpoint in a secret. Obtain the endpoint secret by running the following command:

  ```
  oc get secret -n multicluster-endpoint endpoint-cisctrl-secret -o yaml
  ```

3. Decode the encoded values by completing the following steps:

  1. Decode the access key and secret key by running the following commands:

     ```
     echo <encoded_access_key_value> | base64 --decode

     echo <encoded_secret_key_value> | base64 --decode
     ```

  2. Copy and paste the decoded values into the appropriate fields from the MinIO console.

4. Verify what nodes are labeled `NonCompliant`. From the MinIO menu browser, select **cis-k8s** > **icp-local** > **recent** > **IP address for worker or master node**.

5. Click the non-compliant node and select the attached file to download. Click **Download object** and then open the file. The nodes rules that have failed are shown with the following label: `[FAIL]`.

6. Remediate the non-compliant node manually by referencing the `Remediation` section of the attached file. If a rule does not apply to your environment, add the rule to the `masterNodeExcludeRules` or `workerNodeExcludeRules` in your CIS policy. Your `Remediation` section might resemble the following content:

   ```
   == Remediations ==
   7.9 Reset to the OpenShift defaults

   7.12 Reset to the OpenShift default values.

   7.14 Edit the Openshift node config file /etc/origin/node/node-config.yaml and set RotateKubeletClientCertificate to true.

   7.15 Edit the Openshift node config file /etc/origin/node/node-config.yaml and set RotateKubeletServerCertificate to true.

   8.2
   8.3 Run the below command on each worker node.
   chmod 644 $nodesvc

   8.4 audit test did not run: failed to run: stat -c %U:%G $nodesvc, command: [stat -c %U:%G $nodesvc], error: exit status 1
   ```

### Deleting a CIS poicy

Delete a CIS policy from the CLI or the console.

* Delete a CIS policy from the CLI:
  
  1. Delete a CIS policy by running the following command:

    ```
    kubectl delete policy <cis-policy-name> -n <mcm namespace>  
    ```

    After your policy is deleted, it is removed from your target cluster or clusters.

  2. Verify that your policy is removed by running the following command:

    ```
    kubectl get policy <policy-name> -n <mcm namespace>
    ```
    
* Delete a policy from the console:

  1. From the navigation menu, click **Govern risk** to view a table list of your policies.
  2. Click the **Options** icon for the policy you want to delete in the policy violation table.
  3. Click **Remove**.
  4. From the _Remove policy_ dialog box, click **Remove policy**.

Your policy is deleted.

View a sample of a CIS policy, see _CIS policy sample_ from the [CIS policy controller](cis_policy_ctrl.md) page. For more information about other policy controllers, see [Policy controllers](policy_controllers.md).
