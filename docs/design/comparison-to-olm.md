<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Operand Deployment Lifecycle Manager (ODLM) and Operator Lifecycle Manager (OLM)](#operand-deployment-lifecycle-manager-odlm-and-operator-lifecycle-manager-olm)
  - [What is Operator Lifecycle Manager?](#what-is-operator-lifecycle-manager)
  - [What are operands?](#what-are-operands)
  - [How does the ODLM work?](#how-does-the-odlm-work)
  - [Application Lifecycle Management](#application-lifecycle-management)
    - [installation](#installation)
    - [uninstall](#uninstall)
  - [Dependency Management](#dependency-management)
  - [Additional Features](#additional-features)
    - [Binding Information sharing](#binding-information-sharing)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# Operand Deployment Lifecycle Manager (ODLM) and Operator Lifecycle Manager (OLM)

When it comes to deploying operator services, users will always want to know what is the difference between Operand Deployment Lifecycle Manager (ODLM) and Operator Lifecycle Manager (OLM). In this document, we compare them and discuss what advantages of using ODLM and when you should pick ODLM as an extension of OLM.

## What is Operator Lifecycle Manager?

Operator Lifecycle Manager (OLM) helps users install, update, and manage the lifecycle of Kubernetes native applications (Operators) and their associated services running across their OpenShift Container Platform clusters.
It implements features, like application lifecycle management and dependency management. For more details, you can check [operator-lifecycle-manager](https://github.com/operator-framework/operator-lifecycle-manager)

## What are operands?

Operator is a method of packaging, deploying and managing a Kubernetes application.
Operands are the services and applications that Operator manage.

For example, cert-manager operator deploys a cert-manager deployment, then the cert-manager-operator is an operator and the cert-manager deployment is an operand.

## How does the ODLM work?

The ODLM manages the deployment of the operands for OLM managed operators.  This provides a mechanism for dynamically deploying dependent (and optionally, shared) services in a prescriptive manner and allowing these deployments to interact when and where needed in a scoped fashion.

## Application Lifecycle Management

### installation

- **OLM** can be used to deploy a single operator by creating a Subscription.
- **ODLM** can be used to manage the lifecycle of a group of operands, compared with operator lifecycle manager, **ODLM** focuses on the management of both operands and operators. Users can create an OperandRequest to install a group of Operators and specific CRs for these operators.

### uninstall

- When users use **OLM** only, they need to delete every created custom resource and all the operators.
- When using **ODLM**, if users don't need an operator application anymore, they can delete the OperandRequest created for the application. **ODLM** will handle the logic of checking if there are other users requesting this application and delete both custom resources and operators when they are not required.

## Dependency Management

- **OLM** will automatically create the dependent operator, which provides the CustomResourceDefinition or API that is required by the operator the user wants to install. The dependency management of **OLM** focuses on *API* level dependency, for example, the IAM operator depends on MongoDB operator CRD.
- **ODLM** manages dependency by creating sub-OperandRequest to the creating the dependency operators and operands. The sub OperandRequest can be deployed by **ODLM** as an Operator custom resource or handled by operator logic.  The dependency management of **ODLM** focuses on *Application* level dependency, for example, IAM service depends on MongoDB service. Another gap in the **OLM** dependency management is operator and dependency operators must be in the same scope (OperatorGroup). Users can't make several operators from different scopes depends on a common operator. **ODLM** can handle these cases by creating OperandRequests.

## Additional Features

### Binding Information sharing

**ODLM** can use the OperandBindInfo to claim the information that services want to share with the requester. The ODLM will use the request to copy the secret and/or configmap to the namespace of the OperandRequest. For more details, you can check [OperandBindInfo](./operand-deployment-lifecycle-manager.md#operandbindinfo-spec)
