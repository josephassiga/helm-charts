# Helm Chart to install  Red Hat Advanced Cluster Security for Kubernetes with Openshift GitOps

## Goals
The goal of this Helm charts is to provides Helm charts that will manage the deployment of ACS components into cluster.

## Getting Started
To install ACS different components as follow :

### Install ACS Operator

#### Helm chart acs-operator : 

> [!NOTE]
> This ressource is required on every cluster, it install all CRD.

This chart will create thoses ressources:
- acs-namespaces.yaml : create Namespace `rhacs-operator`
- acs-operatorgroup.yaml: create OperatorGroup `rhacs-operator`
- acs-subscription.yaml: create Subcription `rhacs-operator`
- acs-services.namespaces.yaml: create Namespace `stackrox` where we will install the Central and SecuredCluster components.

#### Helm chart acs-central:

