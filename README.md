<!---

Copyright (c) 2019 AT&T Intellectual Property.

Licensed under the Creative Commons License, Attribution 4.0 Intl.
(the"Documentation License"); you may not use this documentation
except incompliance with the Documentation License. You may obtain
a copy of the Documentation License at 

    https://creativecommons.org/licenses/by/4.0/

Unless required by applicable law or agreed to in writing, 
documentation distributed under the Documentation License is
distributed on an "AS IS"BASIS, WITHOUT WARRANTIES OR CONDITIONS
OF ANY KIND, either express or implied. See the Documentation
License for the specific language governing permissions and
limitations under the Documentation License.

-->

This is a comment, it will not be included)
[comment]: <> (in  the output file unless you use it in)
[comment]: <> (a reference style link.)

*This is a fork of the it-dep repository published by the O-RAN software community.* 

# RIC Deployment Instructions

Clone this repo and update all the submodules within this repo. You might need *root* access.
```
sudo -i
git clone https://github.com/openaicellular/RIC-Deployment.git -b e_rel_xapp_onboarder_support
cd RIC-Deployment
git submodule update --init --recursive --remote
```
The *submodule update* will automatically get all the files from https://github.com/openaicellular/ric-plt-ric-dep.git and update them in the current directory.

## Pre-requisite
The RIC Platform is deployed as a Kubernetes cluster, so we will need to install Kubernetes (version > 1.16.0) and helm (version 2.17.0). We will also need to install a persistent volume  

### 1-node Kubernetes Cluster & Helm Setup 
The *tools* directory contains the required scripts to generate the Kubernetes, docker and helm installation script. Software versions can be changed by modifying the `/tools/k8s/etc/infra.rc` file.

```
cd tools/k8s/bin
./gen-cloud-init.sh
```

This script will generate another script *k8s-1node-cloud-init-k_1_16-h_2_17-d_cur.sh*. Observe that the version of Kubernetes, helm and docker that will be installed in indicated in the name of the generated file **(k_1_16-h_2_17-d_cur)**. Run this script

`./k8s-1node-cloud-init-k_1_16-h_2_17-d_cur.sh`
Once the script is done, the VM will reboot. To check if Kubernetes was successfully installed, run

`sudo kubectl get pods -A`

You should see 9 pods in *kube-system* Namespace and their status should be *Running*.

## Near-Real Time RIC Deployment

## Non-Real Time RIC Deployment

## RIC Aux Cluster Deployment

# RIC Integration
  
This repo contains RAN Intelligent Controller (RIC) deployments related files.


### Overview

The RIC deployment scripts are designed to deploy RIC components using helm charts. A deployment recipe yaml file that
contains parameter key:value pairs can be provided as a parameter for any deployment script in this repository. The
deployment recipe is acting as the helm override values.yaml file. The default parameters are set up to deploy a 
RIC instance using Linux Foundation repositories in a self-contained environment. 


### Directory Structure
.
├── bin
├── ci
├── docs
├── LICENSES.txt	License information
├── README.md           This file
├── RECIPE_EXAMPLE	Directory that contains deploy recipe examples
├── ric-aux		Deployment scripts, charts and configuration files for RIC auxilary functions
├── ric-common		Deployment scripts, charts and configuration files for RIC common template
├── ric-dep	        Deployment scripts, charts and configuration files for RIC platform components
└── tools		Deployment scripts, charts and configuration files for K8S deployment

The deployment scripts are designed to be modularized. Each submodule is managed independently in other Git repo and they can be deployed and undeployed separately. These submodules are coupled together throught the ric-common template which provides common references to naming convention, settings, and configurations. Currently ric-dep is the submodule for RIC platform deployment, and ric-aux is the submodule for the auxilary functions deployment (currently ric-aux is still managed by it/dep repo). In the future, more submodules can be added without changing the structure.

The one-click RIC deployment/undeployment scripts in the ./bin directory will call the deployment/undeployment scripts in the corresponding submodule directory respectively.
In each of the submodule directories, ./bin contains the binary and script files and ./helm contains the helm charts. For the rest of the non-submodule directories please refer to the README.md files in them for more details. 


### To deploy RIC Platform
Choose a deployment recipe (e.g, ./RECIPE_EXAMPLE/PLATFORM/amber_example_recipe.yaml)
Make a copy of the recipe and edit the key:value pairs in it according to your needs
Make sure that you have the correct docker image registry, name, and tag spcified for all the components.
Set the values of extsvcaux/ricip and extsvcaux/auxip to be the external IP addresses of VM hosting RIC cluster and VM hosting AUX cluster, respectively.
Then run the following to deploy:
```sh
$ . ./deploy-ric-platform -f <PATH_TO_YOUR_MODIFIED_RECIPE>
```
Run the following to undeploy:
```sh
$ . ./undeploy-ric-platform 
```

### To deploy RIC Auxiliary functions
Choose a deployment recipe (e.g, ./RECIPE_EXAMPLE/AUX/amber_example_recipe.yaml)
Make a copy of the recipe and edit the key:value pairs in it according to your needs
Set the values of extsvcaux/ricip and extsvcaux/auxip to be the external IP addresses of VM hosting RIC cluster and VM hosting AUX cluster, respectively.
```sh
$ . ./deploy-ric-aux -f <PATH_TO_YOUR_MODIFIED_RECIPE>
```
Run the following to undeploy:
```sh
$ . ./undeploy-ric-aux 
```
