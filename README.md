# Instrumenting legacy Tomcat application for AppDynamics business insights with Intersight Cloud Orchestrator (ICO)
## Contents
        Use Case

        Pre-requisites

        Intersight Target configuration for AppDynamics and on prem entities

        Provision infrastructure and deploy App services with AppDynamics instrumentation

            Step 1: Importing ICO template for App Services Deployment and Instrumentation

            Step 2: Importing ICO template for App Services Load Generation

            Step 3: Setup AppdGlobal Variables

            Step 4: Setup AppdInfra Variables

            Step 5: Setup AppdDb Variables

            Step 6: Setup AppdSaas Variables

            Step 7: Setup AppdRbac Variables

            Step 8: Setup AppdApp Variables

            Step 9: Setup AppdLoad Variables

            Step 10: Execute ICO template for App Services Deployment and Instrumentation

            Step 11: Execute ICO template for App Services Load Generation

            Step 12: View AppDynamics Insights

        Undeploy applications and deprovision infrastructure


### Use Case

* As a DevOps and App developer, use ICO (Intersight Cloud Orchestrator) to enable existing Java/Tomcat Micro services for AppDynamics Insights

* As DevOps and App Developer, use Intersight and AppDynamics to view app and infrastructure insights for Full Stack Observability

This use case addresses the second flow in the below diagram:

![alt text](https://github.com/prathjan/images/blob/main/AppdTomcat3.png?raw=true)

### Pre-requisites

1. The VM template that you provision in Step 5 below will have a user "root/Cisco123" provisioned with sudo privileges. Terraform scripts will use this user credentials to remotely run installation scripts in the VM.

2. Sign up for a user account on Intersight.com. You will need Premier license as well as IWO license to complete this use case. 

3. Sign up for a TFCB (Terraform for Cloud Business) at https://app.terraform.io/. Log in and generate the User API Key. You will need this when you create the TF Cloud Target in Intersight.

4. You will need access to a vSphere infrastructure with backend compute and storage provisioned

5. You will also need an account in AppDynamics SAAS Controller and should have the API Client ID and Client Secret.

6. You will need to have some minimal knowledge of Intersight ICO. Please review tutorials on Youtube as well as the following: https://intersight.com/help/saas/features/orchestration/configure#intersight_cloud_orchestrator

### Intersight Target configuration for AppDynamics and on prem entities

You will log into your Intersight account and create the following targets. Please refer to Intersight docs for details on how to create these Targets:

    Assist

    vSphere

    AppDynamics

    TFC Cloud

    TFC Cloud Agent - When you claim the TF Cloud Agent, please make sure you have the following added to your Managed Hosts. This is in addition to other local subnets you may have that hosts your kubernetes cluster like the IPPool that you may configure for your k8s addressing:
    NO_PROXY URL's listed:

            github-releases.githubusercontent.com

            github.com

            app.terraform.io

            registry.terraform.io

            releases.hashicorp.com

            archivist.terraform.io

### Provision infrastructure and deploy App services with AppDynamics instrumentation


## Step 1: Importing ICO template for App Services Deployment and Instrumentation

Clone the following github repo to get the ICO template:

https://github.com/CiscoDevNet/IcoTemplates.git

Import the template ** Exportlegacy.json ** in Intersight:

![alt text](https://github.com/prathjan/images/blob/main/importleg.png?raw=true)

Review the worflows imported. The workflows leverage IST/TFCB Git repos referenced in Step 2 here: https://developer.cisco.com/codeexchange/github/repo/CiscoDevNet/AppDynamicsTomcatIST

![alt text](https://github.com/prathjan/images/blob/main/summry.png?raw=true)

In short:

SetupLegacyWorkspaces - Sets up the TFCB workspaces for AppdGlobal, AppdDb, AppdInfra, AppdSaas, AppdRbac, AppdApp, AppdLoad, AppdRemove. Details of what these workspaces are used for is enumerated here: https://developer.cisco.com/codeexchange/github/repo/CiscoDevNet/AppDynamicsTomcatIST

UpdateLegacyWorkspaceVars - Updates the variables for the TFCB workspaces

InvokeLegacyPlansBase - Sets up the infrastructure components

InvokeLegacyApps - Installs the Apps with AppDynamics instrumentation

## Step 2: Importing ICO template for App Services Load Generation

Import the template ** ExportOnlyLoad.json ** in Intersight:

![alt text](https://github.com/prathjan/images/blob/main/importload.png?raw=true)

Review the worflows imported:

![alt text](https://github.com/prathjan/images/blob/main/summry2.png?raw=true)

This workflow spins up a VM and runs the application load generator.

## Step 3: Setup AppdGlobal Variables

In Intersight, you will set up the data specific to your environment in this step. 

Open Orchestration-> UpdateLegacyVars->Add AppdGlobal Variables Task:

![alt text](https://github.com/prathjan/images/blob/main/globalvar1.png?raw=true)

Add the following variables, the data will be as it applies to your own specific environment:

vsphere_user - TBD(eg. administrator@vsphere.local)

vm_memory - TBD(eg. 8192)

nbrapm - TBD(eg. 8)

nbrma - TBD(eg. 1)

nbrsim - TBD(eg. 1)

nbrnet(eg. 0)

vm_cpu - TBD(eg. 4)

vm_count - TBD (eg. 1)

appport - TBD(eg. 8085)

vsphere_server - TBD(eg. 10.88.168.24)

datacenter - TBD(eg. Piso14-Lab)

resource_pool - TBD(eg. ccmsuite)

datastore_name - TBD(eg. CCPHXM4)

network_name - TBD(eg. vm-network-6)

template_name - TBD(eg. ubuntu-tmp)

vm_folder - TBD(eg. terraform)

vm_prefix - TBD(eg. terraform-)

vm_domain - TBD(eg. lab14.lc)

Open Orchestration-> UpdateLegacyVars->Add AppdGlobal Variables Sensitive Task:

![alt text](https://github.com/prathjan/images/blob/main/globalvar2.png?raw=true)

Add the following variables, TBD's will be as it applies to your own specific environment:

root_password - TBD

mysql_pass - TBD

vsphere_password - TBD

## Step 4: Setup AppdInfra Variables

Open Orchestration-> UpdateLegacyVars->Add AppdInfra Variables Task:

![alt text](https://github.com/prathjan/images/blob/main/infra.png?raw=true)

Add the following variables, TBD's will be as it applies to your own specific environment:

globalwsname - AppdGlobal

dbvmwsname - AppdDb

org - TBD (eg. Lab14)

## Step 5: Setup AppdDb Variables

Open Orchestration-> UpdateLegacyVars->Add AppdDb Variables Task:

![alt text](https://github.com/prathjan/images/blob/main/db.png?raw=true)

Add the following variables, TBD's will be as it applies to your own specific environment:

globalwsname - AppdGlobal

org - TBD (eg. Lab14)

## Step 6: Setup AppdSaas Variables

Open Orchestration-> UpdateLegacyVars->Add AppdSaas Variables Task:

![alt text](https://github.com/prathjan/images/blob/main/saas.png?raw=true)

Add the following variables, TBD's will be as it applies to your own specific environment:

appname - TBD (eg. MasalaChaiStore)

javaver - 21.5.0.32605

infraver - 21.5.0.1784

machinever - 21.6.0.3155

ibmver - 21.6.0.32801

url - TBD (eg. https://devnet.saas.appdynamics.com)

zerover - 21.6.0.232

Add the following sensitive variables, TBD's will be as it applies to your own specific environment:

clientid - TBD

clientsecret - TBD

## Step 7: Setup AppdRbac Variables

Open Orchestration-> UpdateLegacyVars->Add AppdRbac Variables Task:

![alt text](https://github.com/prathjan/images/blob/main/rbac.png?raw=true)

Add the following variables, TBD's will be as it applies to your own specific environment:

appvmwsname - AppdInfra

saaswsname - AppdSaas

globalwsname - AppdGlobal

org - TBD (eg. Lab14)

## Step 8: Setup AppdApp Variables

Open Orchestration-> UpdateLegacyVars->Add AppdApp Variables Task:

![alt text](https://github.com/prathjan/images/blob/main/app.png?raw=true)

Add the following variables, TBD's will be as it applies to your own specific environment:

appvmwsname - AppdInfra

dbvmwsname - AppdDb

globalwsname - AppdGlobal

org - TBD (eg. Lab14)

## Step 9: Setup AppdLoad Variables

![alt text](https://github.com/prathjan/images/blob/main/load.png?raw=true)

Add the following variables, TBD's will be as it applies to your own specific environment:

appvmwsname - AppdInfra

trigcount - 20

globalwsname - AppdGlobal

org - TBD (eg. Lab14)

## Step 10: Execute ICO Workflow for App Services Deployment and Instrumentation

![alt text](https://github.com/prathjan/images/blob/main/exelegacy.png?raw=true)

You will be prompted for the following data:

![alt text](https://github.com/prathjan/images/blob/main/exeparams.png?raw=true)

Pick up the Agent Pool ID and token from your TFCB account

![alt text](https://github.com/prathjan/images/blob/main/tfcb1.png?raw=true)

![alt text](https://github.com/prathjan/images/blob/main/tfcb2.png?raw=true)

## Step 11: Execute ICO Workflow for App Services Load Generation

![alt text](https://github.com/prathjan/images/blob/main/exeload.png?raw=true)


## Step 12 View AppDynamics Insights

### View Application Insights in AppDynamics 

Checkout the application insights in AppDynamics:

![alt text](https://github.com/prathjan/images/blob/main/appd2.png?raw=true)

![alt text](https://github.com/prathjan/images/blob/main/appd3.png?raw=true)

### View Application Insights in Intersight

Checkout the infrastructure insights in Intersight:

![alt text](https://github.com/prathjan/images/blob/main/appd4.png?raw=true)

### Interfacing with AppDynamics Controller API for De-provisioning - Use RBAC script to remove AppDynamics User and license rule

Execute the AppdRemove workspace to remove all the entities created in AppDynamics.

Due to a known error, you will have to manually delete the MasalaChaiStore application from AppDynamics to complete the cleanup:

![alt text](https://github.com/prathjan/images/blob/main/appd5.png?raw=true)
            
### Undeploy applications and deprovision infrastructure

ICO Workflows do not support deleting TFCB workspaces at this time. So, we will have to terminate and clean up leveraging TFCB.

Destroy the TFCB workspaces in this order:

AppdLoad

AppdRemove

AppdRbac

AppdSaas

AppdInfra

AppdDb
