# Installing while connected online

Red Hat Advanced Cluster Management for Kubernetes is installed using an operator that deploys all of the required components.  

## Prerequisites {#connect_prerequisites}

You must meet the following requirements before you install Red Hat Advanced Cluster Management for Kubernetes:

* Red Hat OpenShift Container Platform version 4.3, or later, must be deployed in your environment, and you must be logged into it with the CLI. See the [OpenShift version 4.3 documentation](https://docs.openshift.com/container-platform/4.3/welcome/index.html) or [OpenShift version 4.4 documentation](https://docs.openshift.com/container-platform/4.4/welcome/index.html).

* A StorageClass is preconfigured in Red Hat OpenShift Container Platform, and is available to create storage for Red Hat Advanced Cluster Management for Kubernetes.

* Your Red Hat OpenShift Container Platform command line interface (CLI) must be version 4.3, or later, and configured to run `oc` commands. See [Getting started with the CLI](https://docs.openshift.com/container-platform/4.3/cli_reference/openshift_cli/getting-started-cli.html) for information about installing and configuring the Red Hat OpenShift CLI.

* Your Red Hat OpenShift Container Platform permissions must allow you to create a namespace.

* You must have an Internet connection to access the dependencies for the operator.

* Your Red Hat OpenShift Container Platform must have access to the Red Hat Advanced Cluster Management operator in the OperatorHub catalog.


## Installing Red Hat Advanced Cluster Management from the CLI

1. Create a namespace where the operator requirements are contained:

    ```
    oc create namespace <namespace>
    ```

    Replace _namespace_ with a name for your namespace.

    **Important:** The Red Hat Advanced Cluster Management operator must be installed in its own namespace. A `ServiceAccount` with a `ClusterRoleBinding` automatically gives cluster administrator privileges to Red Hat Advanced Cluster Management and to any ID with access to the namespace. For security, make sure that anyone who is given access to this namespace already has cluster-administrator access.

2. Switch your project namespace to the one that you created:

    ```
    oc project <namespace>
    ```  
    Replace _namespace_ with the name of the namespace that you created in step 1.

3. Create an operator group. Each namespace can have only one operator group.

    1. Create a `.yaml` file that defines the operator group. Your file should look similar to the following example:

    ```
    apiVersion: operators.coreos.com/v1
    kind: OperatorGroup
    metadata:
      name: <default>
    spec:
      targetNamespaces:
      - <namespace>
    ```

    Replace _default_ with the name of your operator group.
    Replace _namespace_ with the name of your project namespace.

    2. Apply the file that you created to define the operator group:

    ```
    oc apply -f local/<operator-group>.yaml
    ```

    Replace _operator-group_ with the name of the operator group `.yaml` file that you created.

4. Apply the subscription.

    1.  Create a `.yaml` file that defines the subscription. Your file should look similar to the following example:

    ```
    apiVersion: operators.coreos.com/v1alpha1
     kind: Subscription
     metadata:
       name: acm-operator-subscription
     spec:
       sourceNamespace: openshift-marketplace
       source: redhat-operators
       channel: release-1.0
       installPlanApproval: Automatic
       name: advanced-cluster-management
    ```

    2. Apply the subscription:

    ```
    oc apply -f local/<subscription>.yaml
    ```

5. Generate a pull secret to access the entitled content from the distribution registry. **Important:** Pull secrets are namespace-specific, so make sure that you are in the namespace that you created in step 1.

    ```
    oc create secret docker-registry <secret> --docker-server=registry.access.redhat.com/rhacm1-tech-preview --docker-username=<docker_username> --docker-password=<docker_password>
    ```
    Replace _secret_ with the name of the secret that you created.
    Replace _docker_username_ with your username for the distribution registry that you identified as the `docker-server`.
    Replace _docker_password_ with your password or token for the distribution registry that you identified as the `docker-server`.

6. Create the MultiClusterHub custom resource by creating a `.yaml` file that defines the custom resource. Your file should look similar to the following example:

    ```
    apiVersion: operators.open-cluster-management.io/v1beta1
    kind: MultiClusterHub
    metadata:
      name: multiclusterhub
      namespace: <namespace>
    spec:
      imagePullSecret: <secret>
    ```

    Replace _namespace_ with your project namespace.
    Replace _secret_ with the name of the secret that you created.

    If this step fails with the following error, the resources are still being created and applied:

    ```
    error: unable to recognize "./mch.yaml": no matches for kind "MultiClusterHub" in version "operators.open-cluster-                       management.io/v1beta1"
    ```

    Run the command again in a few minutes when the resources are created.

7. View the list of routes after about 10 minutes to find your route:

    ```
    oc get routes
    ```

## Installing Red Hat Advanced Cluster Management from the console

1. Create a namespace for the operator requirements:

    1. In the Red Hat OpenShift Container Platform console navigation, select **Administration** > **Namespaces**.

    2. Select **Create Namespace**.

    3. Provide a name for your namespace. This is the namespace that you use throughout the installation process.

    4. Select **Create**.  

      **Important:** The Red Hat Advanced Cluster Management operator must be installed in its own namespace. A `ServiceAccount` with a `ClusterRoleBinding` automatically gives cluster administrator privileges to Red Hat Advanced Cluster Management and to any ID with     access to the namespace. For security, make sure that anyone who is given access to this namespace already has cluster-administrator     access.

2. Switch your project namespace to the one that you created in step 1. This ensures that the steps are completed in the correct namespace. Some resources are namespace-specific.

    1. In the Red Hat OpenShift Container Platform console navigation, select **Administration** > **Namespaces**.

    2. In the *Projects* field, select the namespace that you created in step 1 from the dropdown list.

3. Create a pull secret that provides the entitlement to the downloads.

    1. In the Red Hat OpenShift Container Platform console navigation, select **Workloads** > **Secrets**.

    2. Select **Create** > **Image Pull Secret**.

    3. Enter a name for your secret.

    4. Select **Image Registry Credentials** as the authentication type.

    5. In the *Registry Server Address* field, enter the address of the distribution registry that contains your image. In most cases, it is `registry.access.redhat.com/rhacm1-tech-preview`.

    6. Enter your username and password or token for the distribution registry that contains the image.

    7. Select **Create** to create the pull secret.

4. Subscribe to the operator.

    1. In the Red Hat OpenShift Container Platform console navigation, select **Operators** > **OperatorHub**.

    2. Select **Red Hat Advanced Cluster Management**. **Tip:** You can filter on the *Integration & Delivery* category to narrow the choices.

    3. Select **Install**.

    4. Update the values, if necessary.

    5. Select **Subscribe**.

5. Create the *MultiClusterHub* custom resource.

    1. In the Red Hat OpenShift Container Platform console navigation, select **Installed Operators** > **MultiClusterHub**.

    2. Select the **MultiClusterHub** tab.

    3. Select **Create MultiClusterHub**.

    4. Update the default values in the `.yaml` file, according to your needs. The following example shows some sample data:

    ```
    apiVersion: operators.open-cluster-management.io/v1beta1
    kind: MultiClusterHub
    metadata:
      name: multiclusterhub
      namespace: <namespace>
    spec:
      imagePullSecret: <secret>
    ```

    Replace _secret_ with the name of the pull secret that you created.
    Confirm that the _namespace_ is your project namespace.

  5. Select **Create** to initialize the custom resource. It can take up to 10 minutes for the hub to build and start.

    After the hub is created, the status for the operator is *Running* on the *Installed Operators* page.

6. Access the console for the hub.

    1. In the Red Hat OpenShift Container Platform console navigation, select **Networking** > **Routes**.

    2. View the URL for your hub in the list, and navigate to it to access the console for your hub.
