# CIS policy controller

You can use the Center for Internet Security (CIS) policy controller to receive notifications about non-compliant clusters. **Note**: The CIS policy controller is only supported on OpenShift Container Platform 3.11.

* [Enabling the CIS policy controller](#enabling-the-cis-policy-controller)
* [CIS policy](#cis-policy)
* [CIS policy elements](#cis-policy-elements)
* [CIS policy controller components](#cis-policy-controller-components)
* [Creating a CIS policy](#creating-a-cis-policy)
* [Viewing a CIS policy](#viewing-a-cis-policy)
* [Remediating CIS policy violation](#remediating-cis-policy-violation)
* [CIS risk score](#cis-risk-score)

## Enabling the CIS policy controller

The CIS policy controller monitors the nodes in a cluster for compliance against CIS Kubernetes benchmark checks. The CIS policies that list the rules to exclude can be applied to the managed clusters. The controller checks the cluster for any violations that are not in the exclude list. 

When you install the Klusterlet, the CIS policy controller is disabled by default. Enable the controller after your cluster is imported by running the following command: 

  ```
  kubectl patch endpointconfig $CLUSTER_NAME -n $CLUSTER_NAMESPACE --type='json' -p='[{"op": "replace", "path": "/spec/cisController/enabled", "value":true}]'
  ```

### Enabling the CIS policy controller feature flag

Complete the following steps to enable the CIS policy controller feature flag. The CIS policy specification becomes available:

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

5. The associated pod restarts. Attempt to create the policy again. See, [Creating a CIS policy from Red Hat Advanced Cluster Management for Kubernetes console](#creating-a-cis-policy-from-red-hat-advanced-cluster-management-for-kubernetes-console).

## CIS policy

The CIS policy controller watches policies of `kind:` `CISPolicy` and updates the status of the policy by checking whether the managed cluster is compliant or not. The policy itself includes a list of CIS controls that need to be excluded from checking. All the CIS controls that are not listed in the policy are checked for compliance. The controller uses `aqua-security` `kube-bench` tool for checking the master and worker nodes in the managed cluster for compliance.

The CIS policy can be created at the Red Hat Advanced Cluster Management for Kubernetes hub or on the managed cluster itself. When created at the Red Hat Advanced Cluster Management for Kubernetes hub, it is embedded into a parent policy. The policy can then be propagated to managed clusters in the Red Hat Advanced Cluster Management for Kubernetes hub based on the placement policy.

The set of rules that constitute CIS controls is different on an OpenShift Container Platform cluster. On OpenShift, the kube-bench tools use rules based on the Red Hat OpenShift Hardening Guide.

### Sample CIS policy 

```yaml
apiVersion: policies.ibm.com/v1alpha1
kind: CisPolicy
metadata:
  labels:
    controller-tools.k8s.io: "1.0"
  name: cispolicy-pci-ocp
spec:
  cisSpecVersion: 1.4
  severity: medium
  kubernetesCisPolicy:
    masterNodeExcludeRules:
    - 1.1 Maintain default behavior for anonymous access
    - 1.3 Insecure Tokens
    - 1.8 Do not expose API server profiling data
    - 1.11 Manage the AlwaysPullImages admission controller
    - 1.12 Use Security Context Constraints instead of DenyEscalatingExec admission
    - 1.13 Use Security Context Constraints instead of the SecurityContextDeny admission controller
    - 1.15 Configure API server auditing - audit log file path
    - 1.16 Configure API server auditing - audit log retention
    - 1.17 Configure API server auditing - audit log backup retention
    - 1.18 Configure audit log file size
    - 1.23 Verify that the service account lookup flag is not set
    - 1.24 Verify the PodSecurityPolicy is disabled to ensure use of SecurityContextConstraints
    - 1.31 Verify that the authorization-mode argument is not set
    - 1.34 Set the encryption provider to aescbc for etcd data at rest
    - 1.35 Enable the EventRateLimit plugin
    - 1.37 Adjust the request timeout argument for your cluster resources
    - 2.1 Verify that Scheduler profiling is not exposed to the web
    - 3.1 Adjust the terminated-pod-gc-threshold argument as needed
    - 3.2 Verify that Controller profiling is not exposed to the web
    - 3.6 Verify that Security Context Constraints are applied to Your Pods and Containers
    - 3.7 Manage certificate rotation
    - 4.6 Verify the scheduler pod specification file ownership set by OpenShift
    - 4.9 Verify the default OpenShift Container Network Interface file permissions
    - 4.12 Verify the default OpenShift etcd data directory ownership
    - 5.1 Verify the default OpenShift cert-file and key-file configuration
    - 5.2 Verify the default OpenShift setting for the client-cert-auth argument
    - 5.3 Verify the OpenShift default values for etcd_auto_tls
    - 5.4 Verify the OpenShift default peer-cert-file and peer-key-file arguments for etcd
    - 5.5 Verify the OpenShift default configuration for the peer-client-cert-auth
    - 5.6 Verify the OpenShift default configuration for the peer-auto-tls argument
    - 5.7 Optionally modify the wal-dir argument
    - 5.8 Optionally modify the max-wals argument
    - 5.9 Verify the OpenShift default configuration for the etcd Certificate Authority
    - 6.1 Ensure that the cluster-admin role is only used where required
    - 6.2 Verify Security Context Constraints as in use
    - 6.3 Use OpenShift projects to maintain boundaries between resources
    - 6.4 Create network segmentation using the Multi-tenant plugin or Network Policies
    - 6.5 Enable seccomp and configure custom Security Context Constraints
    - 6.7 Manage Image Provenance using ImagePolicyWebhook admission controller
    - 6.8 Configure Network policies as appropriate
    - 6.9 Use Security Context Constraints as compensating controls for privileged containers

    workerNodeExcludeRules:
    - 7.1 Use Security Context Constraints to manage privileged containers as needed
    - 7.2 Ensure anonymous-auth is not disabled
    - 7.7 Verify the OpenShift defaults for the protect-kernel-defaults argument
    - 7.9 Verify that the --keep-terminated-pod-volumes argument is set to false
    - 7.10 Verify the OpenShift defaults for the hostname-override argument
    - 7.12 Verify the OpenShift cert-dir flag for HTTPS traffic
    - 8.2 Verify the kubeconfig file ownership of root:root
    - 8.4 Verify the kubelet service file ownership of root:root
    - 8.6 Verify the proxy kubeconfig file ownership of root:root
    - 8.8 Verify the client certificate authorities file ownership of root:root
```

## CIS policy elements

### Policy enforcement 

CIS policy controller can only inform the user about a policy violation. Set the `remediationAction` parameter to `inform`. View an example of a policy in the [Creating a CIS policy]({#create_policy) section.


### masterNodeExcludeRules
The rules applicable to master nodes that are to be exempted from checking. In order for master node to be compliant, this list must include any rules that must be checked manually or those rules that require extra configuration.

### workerNodeExcludeRules
The rules applicable to worker nodes that are to be exempted from checking. In order for worker node to be compliant, this list must include any rules that must be checked manually or those rules that require more configuration.

For more information see, [CIS rules specifications](../governance/cis_policy_rules.md).


## CIS policy controller components

The CIS policy controller consists of the following four components.

### cis-controller-minio

The `cis-controller-minio` object store is used to store the artifacts that are collected by the CIS crawler that runs on all the master and worker nodes. The results from running the aqua-security kube-bench tool are also stored in the CIS Minio object store.

### cis-crawler

The `cis-crawler` collects information about Kubernetes processes, binary files, and configuration files and stores them in the Minio object store. The crawler runs on the master and worker nodes. The crawler runs every 24 hours. 

### drishti-cis

The `drishti-cis` component runs the aqua-security kube-bench tool against the artifacts that are collected by the `cis-crawler` and stores the results in `cis-controller-minio` object store. The artifacts are scanned every 24 hours. 

### cis-controller

The `cis-controller` scans the `cis-controller-minio` object store for results that are generated by the aqua-security kube-bench tool and updates the CIS policy status. The controller scans the results every 24 hours or whenever the policy is updated. 


## Creating a CIS policy

A CIS policy can be created either from the command line by using kubectl or from the console.

* [Creating a CIS policy from the command line](#create_policy_cli)
* [Create a CIS policy from Red Hat Advanced Cluster Management for Kubernetes console](#create_policy_gui)

### Creating a CIS policy from the command line

Complete the following steps to create a certificate policy from the command line interface (CLI):

1. Create a YAML file for your CIS policy by including a set of exclude rules for master node and worker node. See [Creating a YAML file for an Red Hat Advanced Cluster Management for Kubernetes policy](../governance/create_policy.md#yaml) for more information about policy requirements.

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
```

2. Apply the policy by running the following command:
   
   ```
   kubectl apply -f <cis-policy-file> --namespace=<namespace>
   ```

3. Verify and list the policies by running the following command:

   ```
   kubectl get cispolicy --namespace=<namespace>
   ```

### Creating a CIS policy from Red Hat Advanced Cluster Management for Kubernetes console

1. Log in to Red Hat Advanced Cluster Management for Kubernetes console.
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

A policy is created and the CIS policy is embedded into the parent policy. The `.yaml` resembles the following example.

```yaml
apiVersion: policy.mcm.ibm.com/v1alpha1
kind: Policy
metadata:
  name: cis-sample-policy
  namespace: kube-system
  annotations:
    policy.mcm.ibm.com/categories: SystemAndInformationIntegrity
    policy.mcm.ibm.com/controls: ''
    policy.mcm.ibm.com/standards: PCI
    seed-generation: '1'
  finalizers:
    - finalizer.policies.ibm.com
    - propagator.finalizer.mcm.ibm.com
  generation: 1
  resourceVersion: '1906605'
spec:
  complianceType: musthave
  namespaces:
    exclude:
      - kube-*
    include:
      - default
  policy-templates:
    - objectDefinition:
        apiVersion: policies.ibm.com/v1alpha1
        kind: CisPolicy
        metadata:
          name: cis-sample-policy-pci-example
          labels:
            controller-tools.k8s.io: '1.0'
            pci-category: system-integrity
        spec:
          cisSpecVersion: 1.4
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
              1.19 Verify that authorization-mode is not set to AlwaysAllow
              1.20 Verify that the token-auth-file flag is not set
              1.21 Verify the API server certificate authority
              1.22 Verify the API server client certificate and client key
              1.23 Verify that the service account lookup flag is not set
              1.24 Verify the PodSecurityPolicy is disabled to ensure use of SecurityContextConstraints
              1.25 Verify that the service account key file argument is not set
              1.26 Verify the certificate and key used for communication with etcd
              1.27 Verify that the ServiceAccount admission controller is enabled
              1.28 Verify the certificate and key used to encrypt API server traffic
              1.29 Verify that the --client-ca-file argument is not set
              1.30 Verify the CA used for communication with etcd
              1.31 Verify that the authorization-mode argument is not set
              1.32 Verify that the NodeRestriction admission controller is enabled
              1.33 Configure encryption of data at rest in etcd datastore
              1.34 Set the encryption provider to aescbc for etcd data at rest
              1.35 Enable the EventRateLimit plugin
              1.36 Configure advanced auditing
              1.37 Adjust the request timeout argument for your cluster resources
              2.1 Verify that Scheduler profiling is not exposed to the web
              3.1 Adjust the terminated-pod-gc-threshold argument as needed
              3.2 Verify that Controller profiling is not exposed to the web
              3.3 Verify that the --use-service-account-credentials argument is set to true
              3.4 Verify that the --service-account-private-key-file argument is set as appropriate
              3.5 Verify that the --root-ca-file argument is set as appropriate
              3.6 Verify that Security Context Constraints are applied to Your Pods and Containers
              3.7 Manage certificate rotation
              4.1 Verify the OpenShift default permissions for the API server pod specification file
              4.2 Verify the OpenShift default file ownership for the API server pod specification file
              4.3 Verify the OpenShift default file permissions for the controller manager pod specification file
              4.4 Verify the OpenShift default ownership for the controller manager pod specification file
              4.5 Verify the OpenShift default permissions for the scheduler pod specification file
              4.6 Verify the scheduler pod specification file ownership set by OpenShift
              4.7 Verify the OpenShift default etcd pod specification file permissions
              4.8 Verify the OpenShift default etcd pod specification file ownership
              4.9 Verify the default OpenShift Container Network Interface file permissions
              4.10 Verify the default OpenShift Container Network Interface file ownership
              4.11 Verify the default OpenShift etcd data directory permissions
              4.12 Verify the default OpenShift etcd data directory ownership
              4.13 Verify the default OpenShift admin.conf file permissions
              4.14 Verify the default OpenShift admin.conf file ownership
              4.15 Verify the default OpenShift scheduler.conf file permissions
              4.16 Verify the default OpenShift scheduler.conf file ownership
              4.17 Verify the default Openshift controller-manager.conf file permissions
              4.18 Ensure that the controller-manager.conf file ownership is set to root:root (Scored)
              5.1 Verify the default OpenShift cert-file and key-file configuration
              5.2 Verify the default OpenShift setting for the client-cert-auth argument
              5.3 Verify the OpenShift default values for etcd_auto_tls
              5.4 Verify the OpenShift default peer-cert-file and peer-key-file arguments for etcd
              5.5 Verify the OpenShift default configuration for the peer-client-cert-auth
              5.6 Verify the OpenShift default configuration for the peer-auto-tls argument
              5.7 Optionally modify the wal-dir argument
              5.8 Optionally modify the max-wals argument
              5.9 Verify the OpenShift default configuration for the etcd Certificate Authority
              6.1 Ensure that the cluster-admin role is only used where required
              6.2 Verify Security Context Constraints as in use
              6.3 Use OpenShift projects to maintain boundaries between resources
              6.4 Create network segmentation using the Multi-tenant plugin or Network Policies
              6.5 Enable seccomp and configure custom Security Context Constraints
              6.6 Review Security Context Constraints
              6.7 Manage Image Provenance using ImagePolicyWebhook admission controller
              6.8 Configure Network policies as appropriate
              6.9 Use Security Context Constraints as compensating controls for privileged containers
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
          severity: medium
  remediationAction: inform
  disabled: false
```

## Viewing a CIS policy

You can view a CIS policy from the command line by using kubectl or from the console.

### Viewing a CIS policy from command line interface (CLI)

Complete the following steps to view the CIS policy from the managed cluster CLI. View non-compliant policies by completing the following steps:

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

### Viewing a CIS policy from the console 

View any CIS Policy and its status from the console.
1. Log in to the Red Hat Advanced Cluster Management for Kubernetes console.
2. From the navigation menu, click **Govern risk** to view a table list of your policies.

  **Note:** You can filter the table list of your policies by selecting the _All policies_ tab or _Cluster violations_ tab.

3. Select one of your policies.


## Remediating CIS policy violation

Resolve rule failures to remediate your CIS policy violations. Complete the following steps:

1. Log in to your OpenShift Container Platform from the CLI. Run the following command to port-forward the CIS controller MinIO endpoint:

   ```
   oc port-forward endpoint-cisctrl-minio-0 9123:9000
   ```

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
   
## CIS risk score

When a managed cluster is non-compliant, the CIS controller assigns a risk score. Each CIS rule that fails the check is assigned a score. The risk score that is assigned to the non-compliant cluster is the maximum of all the scores that are assigned to failed checks.

The risk score is on a scale of 1 to 10.
*  If the score is less than 4, then the risk category is `low`.
*  If the score is greater than or equal to 4, but less than or equal to 7, then the risk category is `medium`.
*  If the score is greater than 7, then the risk category is `high`.

You can view the risk scores for the CIS rules by running the following command:

  ```
  kubectl -n <namespace> describe configmap endpoint-cisctrl-controller-config
  ```

See [Configuration policy samples](../governance/policy_samples.md) to view policy samples that can be applied to your CIS policies.

For more information about other policy controllers, see [Red Hat Advanced Cluster Management for Kubernetes policy controllers](../governance/policy_controllers.md). 
