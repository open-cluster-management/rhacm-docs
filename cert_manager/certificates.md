# Certificates in Red Hat Advanced Cluster Management for Kubernetes

Various certificates are created and used throughout Red Hat Advanced Cluster Management for Kubernetes.

You can bring your own certificates. You must create a Kubernetes TLS Secret for your secret. After you create your certificates, you can replace certain certificates that are created by the Red Hat Advanced Cluster Management for Kubernetes installer. You must be a cluster administrator or team administrator. 

  **Note**: Replacing certificates is supported only on native Red Hat Advanced Cluster Management for Kubernetes installations.

All certificates required by services that run on Red Hat Advanced Cluster Management for Kubernetes are created during the installation of Red Hat Advanced Cluster Management for Kubernetes. Certificates are either created and managed by the Red Hat Advanced Cluster Management for Kubernetes installer or by the Red Hat Advanced Cluster Management for Kubernetes Certificate manager (`cert-manager`).

See the following topics to replace certificates:

* [Replacing the root CA](cert_root_ca.md)
* [Replacing the management ingress certificates](cert_mgmt_ingress.md)

## Certificates

The following certificates are automatically created during the installation of Red Hat Advanced Cluster Management for Kubernetes:

* Root CA Certificate
* Certificate manager (_cert-manager_)


The Red Hat Advanced Cluster Management for Kubernetes Root CA Certificate is stored within the Kubernetes Secret `multicloud-ca-cert` in the `open-cluster-management` namespace. The certificate can be imported into your client truststores to access Red Hat Advanced Cluster Management for Kubernetes Platform APIs.

### Created and managed by Red Hat Advanced Cluster Management for Kubernetes Certificate manager (_cert-manager_)

View the following table of the internal services that use `cert-manager` to create and manage their certificates: 

| Certificate Name | Issuer Name | Secret Name | Usage |
| ---------------- | ----------- | ----------- | ----- |
| `console-chart-ef680-ca-cert` | `multicluster-hub-mcm-server-ca-issuer` | `console-chart-ef680-uiapi-secrets` | Red Hat Advanced Cluster Management for Kubernetes Console CA cert |
| `grc-59010-ca-cert` | `multicluster-hub-mcm-server-ca-issuer` | `grc-59010-grc-secrets` | Red Hat Advanced Cluster Management for Kubernetes Govern and risk CA cert |
| `kui-proxy` | `multicloud-ca-issuer` | `kui-proxy-secret` | Visual Web Terminal |
| `management-ingress-a0ae5-cert` | `multicloud-ca-issuer` | `management-ingress-c38ac-tls-secret` | Ingress |
| `multicloud-ca-cert` | `cert-manager-rhacm-selfsign` | `multicloud-ca-cert` | Red Hat Advanced Cluster Management for Kubernetes root CA |
| `search-prod-c62a6-aggregator-ca-cert` | `multicloud-ca-issuer` | `search-prod-95fcc-search-secrets` | Search |
| `search-prod-c62a6-redis-ca-cert` | `multicloud-ca-issuer` | `search-prod-95fcc-redisgraph-secrets` | Search |
| `search-prod-c62a6-search-ca-cert` | `multicloud-ca-issuer` | `search-prod-95fcc-search-api-secrets` | Search |
| `topology-276c4-ca-cert` | `multicluster-hub-mcm-server-ca-issuer` | `topology-b24e0-topology-secrets` | Topology |
{: caption="Table 1. Certificates Red Hat Advanced Cluster Management for Kubernetes" caption-side="top"}

Use the certificate policy controller to create and manage certificate policies. See [Red Hat Advanced Cluster Management for Kubernetes policy controllers](../governance/policy_controllers.md) for more for more information about controllers.
