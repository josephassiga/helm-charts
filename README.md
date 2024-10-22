# Helm Chart to install  Red Hat Advanced Cluster Security for Kubernetes with Openshift GitOps

## Goals
The goal of this Helm charts is to provides Helm charts that will manage the deployment of ACS components into cluster.

## Getting Started
To install ACS Operator, you need to install their components as follow :

### Install ACS Operator

#### [Helm chart acs-operator](./rhacs/acs-operator/Chart.yaml) : 

> [!NOTE]
> This ressource is required on every cluster, it install all CRD.
To use this chart, you will need to configure approvalPlan and operator version.

```yaml
# ApprovalPlan values : Manual or Automatic
operator:
  approvalPlan: Automatic
  version: rhacs-operator.v4.5.3
```

This chart will create the following ressources:
- acs-namespaces.yaml : 
    - Create Namespace `rhacs-operator`
- acs-operatorgroup.yaml: 
    - Create OperatorGroup `rhacs-operator`
- acs-subscription.yaml: 
    - Create Subcription `rhacs-operator`
- acs-services.namespaces.yaml: 
    - Create Namespace `stackrox` where we will install the Central and SecuredCluster components.
- acs-operator-installplan-approver-sa.yaml :
    - Create Role `acs-operator-installplan-approver` in the namespaces `rhacs-operator` with rigths `list,get,patch,update` on ressource `installplans`.
    - Create RoleBinding `acs-operator-installer` in the namespaces `rhacs-operator`
    - Create ServiceAccount `acs-operator-installer`
- acs-operator-installplan-approver-job.yaml:
    - Create a Job that will do the following steps:
        - Verify that an ACS InstallPlan exist.
        - Retrieve ACS InstallPlan generated.
        - Approve the InstallPlan.


#### [Helm chart acs-central](./rhacs/acs-central-services/Chart.yaml/):

> [!CAUTION]
> This ressource should only be installed on the cluster where ACS will be installed.

This chart will create the following ressources:
- central-services.yaml : 
    - Create Central ressources `stackrox-central-services` in the namespaces `stackrox`.

#### [Helm chart acs-secured-cluster](./rhacs/acs-secured-cluster/Chart.yaml/):

> [!CAUTION]
> This ressource should only be installed on each cluster to be managed by ACS.
> It is required to have ACS operator installed to have all the CRD.

To use this chart, you will need to get Central endpoint and an API TOKEN.
```yaml
# SecuredCluster Configuration.
clusterName: local-cluster ## use in SecuredCluster ressource  as the value of the clusterName parameter.

central:
  token: ''
  # put this value with https
  endpoint: ''
```
##### Get API TOKEN
![Go to Integrations](./rhacs/images/integrations.png)

![GO to API TOKEN](./rhacs/images/apitoken.png)

![Fill the form](./rhacs/images/apitokenform.png)

##### Get ACS Endpoint
```bash
#Example: central-stackrox.apps.cluster-6gpg5.6gpg5.sandbox2279.opentlc.com
$ oc get route central -n stackrox --no-headers | awk '{print $2}'
```
This chart will create the following ressources:
- create-cluster-init-bundle-sa.yaml :
    - Create Role `rhacs-services` in the namespaces `stackrox` with rigths `list,get,create,patch,update` on ressources `platform.stackrox.io`and `securedClusters`.
    - Create RoleBinding `rhacs-services` in the namespaces `stackrox`
    - Create ServiceAccount `rhacs-services`

- create-cluster-init-bundle-job.yaml:
    - Create a Job that will do the following steps:
        - Verify that central endpoint is available.
        - Retrieve roxctl command line.
        - Generate init bundle for cluster with roxctl command.
        - Create the ressources from the init bundles.
        - Label the securedCluster ressources to trigger the deployement.

- secured-cluster.yaml:
    - Create SecuredCluster `stackrox-secured-cluster-services` in the namespaces `stackrox`.

