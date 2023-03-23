# Build Cluster Requirements
Build cluster is responsible to take a developer's source code commit and apply a supply chain that produces a container image and Kubernetes manifest for deploying '*Run Cluster*' 

## Kubernetes Requirements - Build Cluster
* Supported Kubernetes versions are 1.23, 1.24,1.25
* Default storage class.
* At least 16 GB available memory that is allocatable across clusters, with at least 8 GB per node.
* Logging is enabled and targets the desired application logging platform.
* Monitoring is enabled and targets the desired application observability platform.
* Container Storage Interface (CSI) Driver is installed

## Recommendations - Build Cluster

* Spread across three availability zones (AZs) for high availability.

The Build Cluster includes the following packages (exact list may differ based on supply chain choice):
```
buildservice.tanzu.vmware.com
cartographer.tanzu.vmware.com
cert-manager.tanzu.vmware.com
contour.tanzu.vmware.com
controller.conventions.apps.tanzu.vmware.com
controller.source.apps.tanzu.vmware.com
conventions.appliveview.tanzu.vmware.com
fluxcd.source.controller.tanzu.vmware.com
grype.scanning.apps.tanzu.vmware.com
namespace-provisioner.apps.tanzu.vmware.com 
ootb-supply-chain-testing-scanning.tanzu.vmware.com
ootb-templates.tanzu.vmware.com
scanning.apps.tanzu.vmware.com
spring-boot-conventions.tanzu.vmware.com
tap-auth.tanzu.vmware.com
tap-telemetry.tanzu.vmware.com
tap.tanzu.vmware.com
tekton.tanzu.vmware.com

```
 For Installing  a Build Cluster, we use the following package definition:
```yaml
profile: build
ceip_policy_disclosed: FALSE-OR-TRUE-VALUE # Installation fails if this is not set to true. Not a string.

shared:
  ingress_domain: "INGRESS-DOMAIN"
  kubernetes_distribution: "openshift" # To be passed only for Openshift. Defaults to "".
  kubernetes_version: "K8S-VERSION"
  image_registry:
    project_path: "SERVER-NAME/REPO-NAME" # To be used by Build Service by appending "/buildservice" and used by Supply chain by appending "/workloads".
    username: "KP-DEFAULT-REPO-USERNAME"
    password: "KP-DEFAULT-REPO-PASSWORD"
  ca_cert_data: | # To be passed if using custom certificates.
    -----BEGIN CERTIFICATE-----
    MIIFXzCCA0egAwIBAgIJAJYm37SFocjlMA0GCSqGSIb3DQEBDQUAMEY...
    -----END CERTIFICATE-----

# The above shared keys can be overridden in the below section.

buildservice: # Optional if the corresponding shared keys are provided.
  kp_default_repository: "KP-DEFAULT-REPO"
  kp_default_repository_username: "KP-DEFAULT-REPO-USERNAME"
  kp_default_repository_password: "KP-DEFAULT-REPO-PASSWORD"
supply_chain: testing_scanning
ootb_supply_chain_testing_scanning: # Optional if the corresponding shared keys are provided.
  registry:
    server: "SERVER-NAME"
    repository: "REPO-NAME"
  gitops:
    ssh_secret: "SSH-SECRET-KEY" # (Optional) Defaults to "".
grype:
  namespace: "MY-DEV-NAMESPACE" # (Optional) Defaults to default namespace.
  targetImagePullSecret: "TARGET-REGISTRY-CREDENTIALS-SECRET"
  metadataStore:
    url: METADATA-STORE-URL-ON-VIEW-CLUSTER
    caSecret:
        name: store-ca-cert
        importFromNamespace: metadata-store-secrets
    authSecret:
        name: store-auth-token
        importFromNamespace: metadata-store-secrets
scanning:
  metadataStore:
    url: "" # Configuration is moved, so set this string to empty.
tap_telemetry:
  customer_entitlement_account_number: "CUSTOMER-ENTITLEMENT-ACCOUNT-NUMBER" # (Optional) Identify data for creating Tanzu Application Platform usage reports.
 ```

# Run Cluster Requirements

Run Cluster reads the container image and Kubernetes resources created by the Build Cluster and runs them as defined in the '*Deliverable*' object for each application.

## Kubernetes Requirements - Run Cluster

* Supported Kubernetes versions are 1.23, 1.24, 1.25.
* LoadBalancer for ingress controller (requires 1 external IP address).
* Default storage class.
* At least 16 GB available memory that is allocatable across clusters, with at least 8 GB per node.
* Logging is enabled and targets the desired application logging platform.
* Monitoring is enabled and targets the desired application observability platform.
* Container Storage Interface (CSI) Driver is installed.

## Recommendations - Run Cluster

* Spread across three AZs for high availability.

The Run Cluster includes the following packages:
```
apis.apps.tanzu.vmware.com 
cartographer.tanzu.vmware.com
cert-manager.tanzu.vmware.com
cnrs.tanzu.vmware.com
connector.appliveview.tanzu.vmware.com
contour.tanzu.vmware.com
controller.source.apps.tanzu.vmware.com
eventing.tanzu.vmware.com 
fluxcd.source.controller.tanzu.vmware.com
image-policy-webhook.signing.apps.tanzu.vmware.com
namespace-provisioner.apps.tanzu.vmware.com
ootb-delivery-basic.tanzu.vmware.com
ootb-templates.tanzu.vmware.com
policy.apps.tanzu.vmware.com 
sso.apps.tanzu.vmware.com
service-bindings.labs.vmware.com
services-toolkit.tanzu.vmware.com
tap-auth.tanzu.vmware.com
tap-telemetry.tanzu.vmware.com
tap.tanzu.vmware.com

```
For installing a Run Cluster, we use the following package definition:
```yaml
profile: run
ceip_policy_disclosed: FALSE-OR-TRUE-VALUE # Installation fails if this is not set to true. Not a string.

shared:
  ingress_domain: INGRESS-DOMAIN
  kubernetes_distribution: "openshift" # To be passed only for Openshift. Defaults to "".
  kubernetes_version: "K8S-VERSION"
  ca_cert_data: | # To be passed if using custom certificates.
    -----BEGIN CERTIFICATE-----
    MIIFXzCCA0egAwIBAgIJAJYm37SFocjlMA0GCSqGSIb3DQEBDQUAMEY...
    -----END CERTIFICATE-----
supply_chain: basic

contour:
  envoy:
    service:
      type: LoadBalancer # NodePort can be used if your Kubernetes cluster doesn't support LoadBalancing.

appliveview_connector:
  backend:
    sslDeactivated: TRUE-OR-FALSE-VALUE
    ingressEnabled: true
    host: appliveview.VIEW-CLUSTER-INGRESS-DOMAIN

tap_telemetry:
  customer_entitlement_account_number: "CUSTOMER-ENTITLEMENT-ACCOUNT-NUMBER" # (Optional) Identify data for creating Tanzu Application Platform usage reports.
```

# View Cluster Requirements

Designed to run web applications for Tanzu Application Platform. specifically, Tanzu Learning Center, Tanzu Application Portal GUI and Tanzu API Portal.

## Kubernetes Requirements - View Cluster

* Supported Kubernetes versions are 1.23, 1.24, 1.25.
* LoadBalancer for ingress controller (requires 3 external IP addresses).
* Default storage class.
* At least 16 GB available memory that is allocatable across clusters, with at least 8 GB per node.
* Logging is enabled and targets the desired application logging platform.
* Monitoring is enabled and targets the desired application observability platform.
* Container Storage Interface (CSI) Driver is installed.

## Recommendations - View Cluster

* Spread across three AZs for high availability.
* Utilize a PostgreSQL database for storing user preferences and manually created entities.
* Add Build and all Run Clusters service accounts into View Cluster config yaml to monitor runtime resources of apps in Tanzu Application Platform GUI.

The View Cluster includes the following packages:

```
accelerator.apps.tanzu.vmware.com
api-portal.tanzu.vmware.com
backend.appliveview.tanzu.vmware.com
cert-manager.tanzu.vmware.com
contour.tanzu.vmware.com
controller.source.apps.tanzu.vmware.com
fluxcd.source.controller.tanzu.vmware.com
learningcenter.tanzu.vmware.com
metadata-store.apps.tanzu.vmware.com
tap-auth.tanzu.vmware.com
tap-gui.tanzu.vmware.com
tap-telemetry.tanzu.vmware.com
tap.tanzu.vmware.com
workshops.learningcenter.tanzu.vmware.com

```

For installing a View Cluster, we use the following package definition:
```yaml
profile: view
ceip_policy_disclosed: FALSE-OR-TRUE-VALUE # Installation fails if this is not set to true. Not a string.

shared:
  ingress_domain: "INGRESS-DOMAIN"
  kubernetes_distribution: "openshift" # To be passed only for Openshift. Defaults to "".
  kubernetes_version: "K8S-VERSION"
  ca_cert_data: | # To be passed if using custom certificates.
    -----BEGIN CERTIFICATE-----
    MIIFXzCCA0egAwIBAgIJAJYm37SFocjlMA0GCSqGSIb3DQEBDQUAMEY...
    -----END CERTIFICATE-----

contour:
  envoy:
    service:
      type: LoadBalancer # NodePort can be used if your Kubernetes cluster doesn't support LoadBalancing.

tap_gui:
  service_type: ClusterIP
  app_config:
    catalog:
      locations:
        - type: url
          target: https://GIT-CATALOG-URL/catalog-info.yaml
    kubernetes:
      serviceLocatorMethod:
        type: 'multiTenant'
      clusterLocatorMethods:
        - type: 'config'
          clusters:
            - url: CLUSTER-URL
              name: CLUSTER-NAME # Build profile cluster can go here.
              authProvider: serviceAccount
              serviceAccountToken: CLUSTER-TOKEN
              skipTLSVerify: TRUE-OR-FALSE-VALUE
            - url: CLUSTER-URL
              name: CLUSTER-NAME # Run profile cluster can go here.
              authProvider: serviceAccount
              serviceAccountToken: CLUSTER-TOKEN
              skipTLSVerify: TRUE-OR-FALSE-VALUE

appliveview:
  ingressEnabled: true

tap_telemetry:
  customer_entitlement_account_number: "CUSTOMER-ENTITLEMENT-ACCOUNT-NUMBER" # (Optional) Identify data for creating Tanzu Application Platform usage reports.
 ```

# Iterate Cluster Requirements

Iterate Cluster is for "inner loop" development iteration. Developers connect to the Iterate Cluster via their IDE to rapidly iterate on new software features.

## Kubernetes Requirements - Iterate Cluster

* Supported Kubernetes versions are 1.23, 1.24, 1.25.
* LoadBalancer for ingress controller (2 external IP addresses).
* Default storage class.
* At least 16 GB available memory that is allocatable across clusters, with at least 8 GB per node.
* Logging is enabled and targets the desired application logging platform.
* Monitoring is enabled and targets the desired application observability platform.
* Container Storage Interface (CSI) Driver is installed.


## Recommendations - Iterate Cluster

* Spread across three AZs for high availability.
* Install build profile build service with lite descriptor such as `descriptor_name: "lite"`.

The Iterate Cluster includes the following packages:
```
apis.apps.tanzu.vmware.com 
buildservice.tanzu.vmware.com
backend.appliveview.tanzu.vmware.com
cartographer.tanzu.vmware.com
cert-manager.tanzu.vmware.com
cnrs.tanzu.vmware.com
connector.appliveview.tanzu.vmware.com
conventions.appliveview.tanzu.vmware.com
contour.tanzu.vmware.com
controller.conventions.apps.tanzu.vmware.com
controller.source.apps.tanzu.vmware.com
developer-conventions.tanzu.vmware.com
eventing.tanzu.vmware.com 
fluxcd.source.controller.tanzu.vmware.com
grype.scanning.apps.tanzu.vmware.com
image-policy-webhook.signing.apps.tanzu.vmware.com
namespace-provisioner.apps.tanzu.vmware.com 
ootb-delivery-basic.tanzu.vmware.com
ootb-supply-chain-basic.tanzu.vmware.com
ootb-templates.tanzu.vmware.com
sso.apps.tanzu.vmware.com
service-bindings.labs.vmware.com
services-toolkit.tanzu.vmware.com
spring-boot-conventions.tanzu.vmware.com
tap-auth.tanzu.vmware.com
tap-telemetry.tanzu.vmware.com
tap.tanzu.vmware.com
tekton.tanzu.vmware.com

```

To install a Iterate Cluster, use the following package definition:
```yaml
profile: iterate

shared:
  ingress_domain: "INGRESS-DOMAIN"
  kubernetes_distribution: "openshift" # To be passed only for OpenShift. Defaults to "".
  kubernetes_version: "K8S-VERSION"
  image_registry:
    project_path: "SERVER-NAME/REPO-NAME" # To be used by Build Service by appending "/buildservice" and used by Supply chain by appending "/workloads"
    username: "KP-DEFAULT-REPO-USERNAME"
    password: "KP-DEFAULT-REPO-PASSWORD"
    ca_cert_data: | # To be passed if using custom certificates
    -----BEGIN CERTIFICATE-----
    MIIFXzCCA0egAwIBAgIJAJYm37SFocjlMA0GCSqGSIb3DQEBDQUAMEY...
    -----END CERTIFICATE-----

ceip_policy_disclosed: FALSE-OR-TRUE-VALUE # Installation fails if this is not set to true. Not a string.

# The above shared keys may be overridden in the below section.

buildservice: # Optional if the corresponding shared keys are provided.
  kp_default_repository: "KP-DEFAULT-REPO"
  kp_default_repository_username: "KP-DEFAULT-REPO-USERNAME"
  kp_default_repository_password: "KP-DEFAULT-REPO-PASSWORD"

supply_chain: basic
ootb_supply_chain_basic: # Optional if the shared above mentioned shared keys are provided.
  registry:
    server: "SERVER-NAME"
    repository: "REPO-NAME"
  gitops:
    ssh_secret: "SSH-SECRET-KEY" # (Optional) Defaults to "".

image_policy_webhook:
  allow_unmatched_tags: true

contour:
  envoy:
    service:
      type: LoadBalancer # (Optional) Defaults to LoadBalancer.

cnrs:
  domain_name: "TAP-ITERATE-CNRS-DOMAIN" # Optional if the shared.ingress_domain is provided.

appliveview_connector:
  backend:
    sslDeactivated: TRUE-OR-FALSE-VALUE
    ingressEnabled: true
    host: appliveview.VIEW-CLUSTER-INGRESS-DOMAIN

tap_telemetry:
  customer_entitlement_account_number: "CUSTOMER-ENTITLEMENT-ACCOUNT-NUMBER" # (Optional) Identify data for creating Tanzu Application Platform usage reports.
```
