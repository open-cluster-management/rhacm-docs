# Red Hat Advanced Cluster Management for Kubernetes policy overview

A Red Hat Advanced Cluster Management for Kubernetes template is defined within a policy document. Each policy document can have at least one or multiple templates.

**Important**: Each client and provider is responsible for ensuring that their managed cloud environment meets internal enterprise security standards for software engineering, secure engineering, resiliency, security, and regulatory compliance for workloads hosted on Kubernetes clusters. Use the governance and security capability to gain visibility and remediate configurations to meet standards.

## Policy elements {#policy_elements_2}

Each _policy_ within the policy document contains the following elements:

  - `annotations` parameter is used to specify a set of security details that describes the set of standards the policy is trying to validate. View the following descriptions of the security policy annotations:
      * `policy.mcm.ibm.com/standards` - The name or names of security standards the policy is related to. For example, National Institute of Standards and Technology (NIST) and Payment Card Industry (PCI).
      * `policy.mcm.ibm.com/categories` - A security control category represent specific requirements for one or more standards. For example, a System and Information Integrity category might indicate that your policy contains a data transfer protocol to protect personal information, as required by the HIPAA and PCI standards.
      * `policy.mcm.ibm.com/controls` -  The name of the security control that is being checked. For example, Center of Internet Security (CIS) and certificate policy controller.

        **Notes**:

        * Parameter values for `standard, categories, and controls` are not required. The parameter value is empty when the values are not defined.
        * Use your internal security standards or industry standards for the `annotations` field. You can view policy violations based on the standards and categories that you define for your policy on the _Policies_ page, from the console.
        * You must add your `label` directly into the YAML file to create a label.

   - `disabled` parameter provides the ability to enable and disable your policies.
   
  - `remediationAction` parameter specifies the remediation of your policy. The parameter values are _enforce_ and _inform_. The _inform_ value reports whether the cluster is compliant to the specified policies. You must remediate your policy manually. The _enforce_ value automatically remediate the violations on the target managed clusters.

      **Note**: For example, with `remediationAction` set to _inform_ and a policy that requires a specific role-based access control (RBAC), the policy status reports that the cluster is non-compliant if the role is not on the cluster. The policy includes the list of violations. No remediation actions are carried out in _inform_ mode.

      With `remediationAction` set to _enforce_, the Red Hat Advanced Cluster Management for Kubernetes policy manager automatically creates the missing policy on the target managed clusters.
  
  - `namespace` selector specifies which namespaces within the hub cluster that the policy is applied to.

    **Note**: A namespace that is specified in the object template of a policy controller, overrides the namespace in the corresponding parent policy.


  - A list of `templates`, such as `role-templates`, `object-templates`, and `policy-templates` within the policy that describes how a resource in Kubernetes might be defined, and whether it is allowed to exist.

    - A `role-template` is used to list RBAC roles that must be evaluated or applied to the managed clusters. Role templates are treated as a special category of templates, as they have rules inside that can be analyzed and compared to evaluate the compliance of a cluster.

    - An `object-template` is used to list any other Kubernetes object that must be evaluated or applied to the managed clusters. An example of object can be a pod security policy, an image policy, or a limit range.

    - A `policy-template` is used to create one or more policies for third party or external security controls. For example, you can create a certificate expiration policy with the certificate policy controller.

   Create custom policy controllers to validate the compliance of your policies. See, [Creating a Red Hat Advanced Cluster Management for Kubernetes security policy](../create_policy.md) to create a policy. For more information about other policy controllers, see [Red Hat Advanced Cluster Management for Kubernetes policy controllers](../governance/policy_controllers.md).

For more information on how the policy elements are utilized, see [Red Hat Advanced Cluster Management for Kubernetes policy example](policy_example.md). See [Red Hat Advanced Cluster Management for Kubernetes Governance and risk](compliance_intro.md) for more policy topics.
