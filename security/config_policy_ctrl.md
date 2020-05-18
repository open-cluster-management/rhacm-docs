# Kubernetes configuration policy controller

Configuration policy controller can be used to configure any Kubernetes resource and apply security policies across your clusters. 

The configuration policy controller communicates with the local Kubernetes API server to get the list of your configurations that are in your cluster. For more information about CRDs, see [Extend the Kubernetes API with CustomResourceDefinitions](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/). 

<!--Parent policy  vs policy (replicated policy?)-->

The configuration policy controller supports the `enforce` feature and monitors the compliance of the following policies:

* Memory usage policy
* Namespace policy
* Image vulnerability policy
* Pod nginx policy
* Pod security policy
* Role policy
* Rolebinding policy
* Security content constraints (SCC) policy

Each configuration policy supports the `enforce` feature. 

## Configuration policy controller YAML structure

```yaml
Name:         configuration-policy-example
Namespace:
Labels:
APIVersion:   policies.ibm.com/v1alpha1
Kind:         ConfigPolicy
Metadata:
  Finalizers:
    finalizer.mcm.ibm.com
Spec:
  Conditions:
    Ownership:
    NamespaceSelector:
      Exclude:
      Include:
    RemediationAction:
    Disabled:
 Status:
   CompliancyDetails:
     Configuration-Policy-Example:
       Default:
       Kube - Public:
   Compliant:          Compliant
 Events:
```

## Configuration policy controller YAML table

|Field|Description|
|-- | -- |
| Name | Required. <!--Add explanation--> |
| Namespace | Required. <!--Add explanation--> |
| Labels | Optional. <!--Add description--> |
| APIVersion | Required. Set the value to `mcm.ibm.com/v1alpha1`. <!--current place holder until this info is updated--> |
| Kind | Required. Set the value to `Policy` to indicate the type of policy. |
| Metadata | Required. <!--add description--> |
| Metadata.Finalizers | Required. <!--add description-->  |
| Spec | Required. Specifications of which configuration policy to monitor and how to remediate them. |
| Spec.Conditions |  Required. <!--add description--> |
| Spec.Ownership | Required. <!--Add description--> |
| Spec.NamespaceSelector| Required. <!--add description--> parameter values for `Include` and `Exclude`. |
| Spec.RemediationAction | Required. | <!--add description-->|
| Spec.Disabled | Required. Set the value to `true` or `false`. The `disabled` parameter provides the ability to enable and disable your policies.|
| Spec.Status | Required. Reports the status of the policy. <!--expand explanation if possible--> |
| Status.CompliancyDetails | Required. <!--details needed--> |
| Status.Events| Required. <!--add details-->
{: caption="Table 1. Required and optional definition fields" caption-side="top"}


## Configuration policy YAML structure

```yaml
   apiVersion: policy.mcm.ibm.com/v1alpha1
   kind: Policy
   metadata:
     name: 
     namespace:
   spec:
     namespaces:
       include:
       exclude:
     remediationAction:
     disabled:
     complianceType:
     object-templates:
      ...

   ```

<!--Still need to add doc to create a custom controller #1224-->
Learn about how policies are applied on your hub cluster. See [Policy samples](policy_sample_intro.md) for more details. Learn how to create and customize policies, see [Manage security policies](manage_policy_overview.md). 

See [Policy controllers](policy_controllers.md) for more information about controllers.
