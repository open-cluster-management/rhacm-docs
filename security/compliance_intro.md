# Governance and risk

Use the Governance and risk page to create, view, and manage your security policies. You can view the number of policy violations in your clusters and applications. 

From the _Policies_ page, you can view a summary of cluster and policy violations. You can customize your _Summary_ view by filtering the violations by categories or standards. Collapse the summary to see less information. You can also search for policies. 

  **Note**: When you search for a policy in the hub cluster, you might also receive the name of the replicated policy on your managed cluster. For example, if you search for `policy-dhaz-cert`, the following policy name from the hub cluster might appear: `default.policy-dhaz-cert`. The name of the replicated policy might appear as the following name: `namespace-in-hub.policyname`.

You can view a table list of violations. The following details are provided in the table: description, resources, severity, cluster, standards, controls, categories, and update time. You can filter the violation table view by policies or cluster violations.


Learn about the structure of an Red Hat Advanced Cluster Management for Kubernetes policy, and how to use the Red Hat Advanced Cluster Management for Kubernetes Governance and risk dashboard.

- [Policy overview](policy_example.md)
- [Policy controllers](policy_controllers.md)
- [Policy samples](policy_sample_intro.md)
- [Manage security policies](manage_policy_overview.md)
