# Enabling AppDynamics Business Insights for a containerized Multi Service Kubernetes Application with Cisco Intersight Service for Terraform 
## Contents
        Use Case

        Pre-requisites

        Pre provisioning Steps

            Step 1: Intersight Target configuration for AppDynamics and on prem entities

            Step 2: Setting up TFCB Workspaces

            Step 3: Share variables with a Global Workspace

            Step 4: Prepping infrastructure & platform for application deployment

        Interfacing with AppDynamics Controller API for Provisioning

            Step 5: Use RBAC script to create AppDynamics User and license rule

            Step 6: Install k8s metrics server

            Step 7: Install Kubernetes Cluster Agent

        Deploying App Services followed by automated instrumentation of AppDynamics agents

            Step 8: Deploy application registry services

            Step 9: Deploy application services

        Generate Application Load

        View Application Insights in AppDynamics and Intersight

        Interfacing with AppDynamics Controller API for De-provisioning
        
            Step 10: Undeploy application services

            Step 11: Undeploy AppDynamics Cluster Agent

            Step 12: Deprovision k8s cluster

        


### Use Case

* As a DevOps and App developer, use IST (Intersight Service for Terraform) to enable existing containerized k8s services for AppDynamics Insights

* As DevOps and App Developer, use Intersight and AppDynamics to get app and infrastructure insights for Full Stack Observability

This use case addresses the first flow in the below diagram: 

![alt text](https://github.com/prathjan/images/blob/main/tomflow.png?raw=true)

### Pre-requisites

1. The VM template that you provision in Step 5 below will have a ubuntu image with user "root/Cisco123" provisioned with sudo privileges. Terraform scripts will use this user credentials to remotely run installation scripts in the VM.

2. Sign up for a user account on Intersight.com. You will need Premier license as well as IWO license to complete this use case. 

3. Sign up for a TFCB (Terraform for Cloud Business) at https://app.terraform.io/. Log in and generate the User API Key. You will need this when you create the TF Cloud Target in Intersight.

4. You will need access to a vSphere infrastructure with backend compute and storage provisioned

5. You will also need an account in AppDynamics SAAS Controller and should have the API Client ID and Client Secret.


7. You will create an account on containers.cisco.com and save the username and password. This is to download the docker images from the repo.

![alt text](https://github.com/prathjan/images/blob/main/repo.png?raw=true)

### Step 1: Intersight Target configuration for AppDynamics and on prem entities

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

### Step 2: Setting up TFCB Workspaces

1. 

You will set up the following workspaces in TFCB and link to the VCS repos specified. 

    tfiksglobal -> https://github.com/CiscoDevNet/tfiksglobal.git

    tfikshost -> https://github.com/CiscoDevNet/tfikshost.git

    tfiksrbac -> https://github.com/CiscoDevNet/tfiksrbac.git

    tfiksmetrics -> https://github.com/CiscoDevNet/tfiksmetrics.git

    tfiksclagent -> https://github.com/CiscoDevNet/tfiksclagent.git

    tfiksteareg -> https://github.com/CiscoDevNet/tfiksteareg.git

    tfiksteaapp -> https://github.com/CiscoDevNet/tfiksteaapp.git

    tfiksload -> https://github.com/CiscoDevNet/tfiksload.git 

    tfiksremove -> https://github.com/CiscoDevNet/tfiksremove.git


2. 

You will set up the tfiksglobal workspace here. 

You will set the following variables:

appport - eg. 30080	for the Teastore app

nbrapm - eg. 8	for the Teastore app

nbrma - eg. 1	for the Teastore app

nbrsim - eg. 1	for the Teastore app

nbrnet - eg. 0 for the Teastore app

privatekey - your ssh private key that you used to provision your IKS cluster nodes

url - eg. https://devnet.saas.appdynamics.com

account	- eg. devnet

namespaces	- eg. default

username	- eg. TBD, just enter a random str

password	- eg. TBD, just enter a random str

dockeruser - your user name for containers.cisco.com

dockerpass - your password for containers.cisco.com

storename - your store name. eg. IKSChaiStore

Please also set this workspace to share its data with other workspaces in the organization by enabling Settings->General Settings->Share State Globally.

3. 

You will set up the tfikshost workspace here
Set Execution Mode as Remote.Please also set this workspace to share its data with other workspaces in the organization by enabling Settings->General Settings->Share State Globally.

You will set the following variables:

ikswsname - eg. sb_iks, which is the workspace that created the IKS k8s cluster

org - TFCB organization like "CiscoDevNet" or "Lab14"

4. 

You will set up the tfiksrbac workspace here.

Set Execution Mode as Agent and select the TF Cloud Agent that you have provisioned.

You will set the following variables:

ikswsname - eg. sb_iks, which is the workspace that created the IKS k8s cluster	

org - eg. Lab14, which is the TFCB organization	
	
hostwsname - tfikshost	

globalwsname - tfiksglobal

5. 

You will set up the tfiksmetrics workspace here.

Set Execution Mode as Agent and select the TF Cloud Agent that you have provisioned.

You will set the following variables:

org - eg. Lab14	

ikswsname - eg. sb_iks	


6. 

You will set up the tfiksclagent workspace here.

Set Execution Mode as Agent and select the TF Cloud Agent that you have provisioned.

You will set the following variables:

org - TFCB organization like "CiscoDevNet" or "Lab14"

ikswsname - eg. sb_iks

globalwsname - tfiksglobal

hostwsname	- tfikshost

7. 

You will set up the tfiksteareg workspace here.

Set Execution Mode as Agent and select the TF Cloud Agent that you have provisioned.

You will set the following variables:

org - TFCB organization like "CiscoDevNet" or "Lab14"

ikswsname - eg. sb_iks


8. 

You will set up the tfiksteaapp workspace here.

Set Execution Mode as Agent and select the TF Cloud Agent that you have provisioned.

You will set the following variables:

org - TFCB organization like "CiscoDevNet" or "Lab14"

ikswsname - eg. sb_iks

9. 

You will set up the tfiksload workspace here.

Set Execution Mode as Agent and select the TF Cloud Agent that you have provisioned.

You will set the following variables:

hostwsname	- tfikshost

globalwsname - tfiksglobal

org - TFCB organization like "CiscoDevNet" or "Lab14"

trigcount - trigger count, set to some random number

10. 

You will set up the tfiksremove workspace here.

Set Execution Mode as Agent and select the TF Cloud Agent that you have provisioned.

You will set the following variables:

hostwsname	- tfikshost

globalwsname - tfiksglobal

org - TFCB organization like "CiscoDevNet" or "Lab14"


### Step 3: Share variables with a Global Workspace

Execute the tfiksglobal TFCB workspace to setup the global variables for other workspaces. Check for a sucessful Run before progressing to the next step.
        
### Step 4: Prepping infrastructure & platform for application deployment

In this step, you will set up the k8s cluster to host the application containers. You will follow the directions in this Code Exchange entry to create a IKS (Intersight Kubernetes Service) k8s cluster:

https://developer.cisco.com/codeexchange/github/repo/CiscoDevNet/intersight-tfb-iks

Also, Execute the tfikshost TFCB workspace to setup the master node to run some of the utility functions needed in this deployment. This will be needed to access the application in Step 9.

Check for a sucessful Run before progressing to the next step. Also, make a note of the master node IP address.

### Step 5: Use RBAC script to create AppDynamics User and license rule

Execute the tfiksrbac TFCB workspace to set up the user/role/license rules in SAAS Controller. This will also create the k8s secret for the accesskey that will used to install the cluster agent in the cluster. Check for a sucessful Run before progressing to the next step.

### Step 6: Install k8s metrics server

Execute the tfiksmetrics TFCB workspace to set up the metrics server which the cluster agent will levarage in the next step. Check for a sucessful Run before progressing to the next step.

### Step 7: Install Kubernetes Cluster Agent

Execute the tfiksclagent TFCB workspace to install the cluster agent in the k8s cluster to automatically instrument the Java services. Check for a sucessful Run before progressing to the next step.

### Step 8: Deploy application registry services 

Execute the tfiksteareg TFCB workspace to deploy the application registry service which is a dependency for the other app services. Check for a sucessful Run before progressing to the next step.

### Step 9: Deploy application services 

Execute the tfiksteaapp TFCB workspace to deploy the application registry service which is a dependency for the other app services. Check for a sucessful Run before progressing to the next step.

View the application deployment status at:

http://<master_node_ip>:30080/tools.descartes.teastore.webui/status

![alt text](https://github.com/prathjan/images/blob/main/teastatus.png?raw=true)

View the application at:

http://<master_node_ip>:30080/tools.descartes.teastore.webui/

![alt text](https://github.com/prathjan/images/blob/main/tea.png?raw=true)


### Step 9: Generate Application Load

Execute the tfiksload workspace to generate load for the apps deployed

### View Application Insights in AppDynamics 

Checkout the application insights in AppDynamics:

![alt text](https://github.com/prathjan/images/blob/main/appd.png?raw=true)

### View Application Insights in Intersight

Checkout the infrastructure insights in Intersight:

![alt text](https://github.com/prathjan/images/blob/main/optimize.png?raw=true)

### Interfacing with AppDynamics Controller API for De-provisioning - Use RBAC script to remove AppDynamics User and license rule

Execute the tfiksremove workspace to remove all the entities created in AppDynamics.

Due to a known error, you will have to manually delete the IKSChaiStore application from AppDynamics to complete the cleanup:

![alt text](https://github.com/prathjan/images/blob/main/appddel.png?raw=true)
            
### Undeploy applications and deprovision infrastructure

Destroy the TFCB workspaces in this order:

tfiksload

tfiksremove

tfiksrbac

tfiksteaapp

tfiksteareg

tfiksmetrics

tfiksclagent

tfikshost

tfiksglobal

