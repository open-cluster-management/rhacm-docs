# Certificate policy controller

Certificate policy controller can be used to detect certificates that are close to expiring. Configure and customize the certificate policy controller by updating the minimum duration parameter in your controller policy. When a certificate expires in less than the minimum duration amount of time, the policy becomes noncompliant. The certificate policy controller is created on your hub cluster.

The certificate policy controller communicates with the local Kubernetes API server to get the list of secrets that contain certificates and determine all non-compliant certificates. For more information about CRDs, see [Extend the Kubernetes API with CustomResourceDefinitions](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/). 

Certificate policy controller does not support the `enforce` feature. 

## Certificate policy controller YAML structure

View the following example of a certificate policy and review the element in the YAML table:

  ```yaml
  Name:         certificate-policy-example
  Namespace:
  Labels:       category=system-and-information-integrity
  APIVersion:  policies.ibm.com/v1alpha1
  Kind:         CertificatePolicy
  Metadata:
  Spec:
    Conditions:
      Ownership:
    NamespaceSelector:
      Exclude:
      Include:
    RemediationAction:
    Disabled:
    MinimumDuration:
 Status:
   CompliancyDetails:
     Certificate-Policy-Example:
       Default:
       Kube - Public:
   Compliant:
  Events:
  ```

### Certificate policy controller YAML table

|Field|Description|
|-- | -- |
| Name | Required. <!--Add explanation--> |
| Namespace | Required. <!--Add explanation--> |
| Labels | Optional. In a certificate policy, the `category=system-and-information-integrity` label categorizes the policy and facilitates querying the certificate policies. If there is a different value for the `category` key in your certificate policy, the value is overridden by the certificate controller. |
| APIVersion | Required. Set the value to `policies.ibm.com/v1alpha1`. <!--current place holder until this info is updated--> |
| Kind | Required. Set the value to `CertificatePolicy` to indicate the type of policy. |
| Metadata | Required. <!--add description--> |
| Spec | Required. Specifications of which certificates to monitor and refresh.|
| Spec.Conditions |  Required. <!--add description--> |
| Spec.Ownership | Required. <!--Add description--> |
| Spec.NamespaceSelector| Required. Namespace where you want the policy to be applied to. Enter parameter values for `Include` and `Exclude`. **Note**: When you create multiple certificate policies and apply them to the same managed cluster, each policy `NamespaceSelector` must be assigned a different value.|
| Spec.RemediationAction | Required. | <!--add description-->|
| Spec.Disabled | Required. Set the value to `true` or `false`. The `disabled` parameter provides the ability to enable and disable your policies.|
| Spec.MinimumDuration | Required. parameter specifies the smallest duration before a certificate is considered non-compliant. When the certificate expiration is greater than the `minimumDuration`, then the certificate is considered compliant. <!--is there a default parameter value-->| 
| Status | Required. Reports the status of the policy. <!--expand explanation if possible--> |
| Status.CompliancyDetails | Required. <!--details needed--> |
| Status.Events| Required. <!--add details-->
{: caption="Table 1. Required and optional definition fields" caption-side="top"}


## Certificate policy sample

When your certificate policy controller is created on your hub cluster, a replicated policy is created on your managed cluster. Your certificate policy on your managed cluster might resemble the following file:

```yaml
apiVersion: policies.ibm.com/v1alpha1
kind: CertificatePolicy
metadata:
  name: certificate-policy-1
  namespace: kube-system
  label:
    category: "System-Integrity"
spec:
  namespaceSelector:
    include: ["default", "kube-*"]
    exclude: ["kube-system"]
  remediationAction: inform
  disabled: false
  minimumDuration: 100h
```

Learn how to create a certificate policy, see [Manage security policies](manage_policy_overview.md) for more details.
