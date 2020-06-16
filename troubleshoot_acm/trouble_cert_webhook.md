# Troubleshooting cert-manager-webhook

Cert-manager is deployed with a webhook to support RBAC and provide extra validation when `cert-manager` resources are created.

## Symptom: cert-manager-webhook

You might see one of the following errors:

```
error: unable to retrieve the complete list of server APIs: admission.certmanager.io/v1beta1: Unauthorized
```

```
failed calling webhook "certificates.admission.certmanager.io": the server is currently unable to handle the request
```

## Resolving the problem: cert-manager-webhook

1. Check the following items:

    - Run the following command to see whether the `webhook` and `cainjector` pods are available, Get the pod names and status:

      ```
      kubectl get pods -n cert-manager | grep webhook
      ```

    - If the pods are not available, run the following command on each pod and note the errors:

      ```
      kubectl describe <pod name> -n cert-manager
      ```

    - If the pods are missing volume mounts or secrets, check the `cert-manager` namespace for messages that resemble the following:

      ```
      Unable to mount volumes for pod "cert-manager-webhook-ibm-cert-manager-webhook-998687778-4tmlz_cert-manager(52d4e997-ce58-11e9-94c3-06ad18c6690e)": timeout expired waiting for volumes to attach or mount for pod "cert-manager"/"cert-manager-webhook-ibm-cert-manager-webhook-998687778-4tmlz". list of unmounted volumes=[certs]. list of unattached volumes=[certs default-token-8gsfx]
      ```

      ```
      MountVolume.SetUp failed for volume "certs" : secrets "cert-manager-webhook-ibm-cert-manager-webhook-webhook-tls" not found
      ```

    - Check for the namespace label `certmanager.io/disable-validation=true`. View the cert-manager namespace:

      ```
      kubectl describe namespace cert-manager
      ```

      The output is similar to the following example. Take note of the label:

      ```
      $ kubectl describe namespace cert-manager
       Name:         cert-manager
       Labels:       certmanager.k8s.io/disable-validation=true
                 icp=system
       Annotations:  mcm.ibm.com/accountID: id-mycluster-account
                 mcm.ibm.com/type: System
       Status:       Active
       No resource quota.

       Resource Limits
       Type       Resource  Min  Max   Default Request  Default Limit  Max Limit/Request Ratio
       ----       --------  ---  ---   ---------------  -------------  -----------------------
       Container  cpu       0    800m  300m             800m           -
       Container  memory    0    1Gi   300Mi            1Gi            -
      ```

    - If the label is not on the namespace, add the label and restart the `cert-manager` webhook pods. Add the label:

      ```
      kubectl label namespace cert-manager certmanager.io/disable-validation=true
      ```

      ```
      kubectl delete <pod name> -n cert-manager
      ```

    - If the label does exist on the namespace, check the normal `cert-manager` pods and `cert-manager` logs. Ensure the pods are up and running and check for any errors in the logs.

      Get the pod names and statuses:

      ```
      kubectl get pods -n cert-manager
      ```

      Check the logs:

      ```
      kubectl logs -n cert-manager <pod name>
      ```

2. If step 1 does not apply, and everything is up and running, then restart the webhook pods with the following command:

   ```
   kubectl delete <pod name> -n cert-manager
   ```

3. If you continue to receive errors, check the Kubernetes apiserver for the following:

    - Ensure the apiserver is active.
    - Check whether requests are sent by the apiserver.
    - Ensure the `ValidatingAdmissionWebhook` is enabled.

         - For environments outside of Red Hat Advanced Cluster Management for Kubernetes, check to see whether you specified `--enable-admission-plugins=` in `kube_apiserver_extra_args` in your `config.yaml` file. 
         - If you did not, no action is required. If you did, ensure that `ValidatingAdmissionWebhook` displays in the list.
         - For Red Hat OpenShift environments, check the `master-config.yaml`. If `admissionConfig.pluginConfig` does not contain `ValidatingAdmissionWebhook`, you must add it.

