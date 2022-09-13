#**ISD Installation Guide**#
##**Introduction**##
This document provides the step-by-step instructions for installing ISD on a kubernetes cluster. Some basic knowledge of command-line interface is required.
Before you start, it might be helpful to go through these documents:

* Routing Web URLs to ISD services - Refer [here](https://docs.opsmx.com/products/opsmx-intelligent-software-delivery-platform/opsmx-isd-installation/routing-web-urls-to-isd-services)
* ISD On-Prem POV Infrastructure requirements - Refer [here](https://docs.opsmx.com/products/opsmx-intelligent-software-delivery-platform/opsmx-isd-installation/isd-on-prem-pov-infrastructure-requirements)
* ISD - Commonly used Commands - Refer [here](https://docs.opsmx.com/products/opsmx-intelligent-software-delivery-platform/opsmx-isd-installation/isd-commonly-used-commands)
* ISD Service Catalogue - Refer [here](https://docs.opsmx.com/products/opsmx-intelligent-software-delivery-platform/opsmx-isd-installation/isd-service-catalogue)

##**Pre-requisites**##
* A laptop/machine being used to install that has the required software mentioned here
* A kubeconfig file to access the kubernetes cluster
* A working “kubectl” command. Execute the command below:
	* `kubectl get no` to see the nodes
	* `kubectl get ns` to see the namespaces
* These commands should show some output. If required, rename the kubeconfig file 
as "config" and copy to .kube folder in your machine
* A github repo, and a “personal access token”. Instructions for creating these can be found here.
* NGINX Ingress Controller and cert-manager(if using https) in the cluster. 

##**Installation steps**##
###**Setup URLs**###
1. Host names that will be used to access ISD: Three host-names are required as mentioned below:

	* oes.<subdomain>.company.com
	* spin. <subdomain>.company.com
	* oes-gate.<subdomain>.company.com

2. Run the following command to get the IP address of the ingress controller

	`kubectl get ingress -n ingress-nginx`

###**Prepare gitops repo**###
Prepare your gitops repository as follows:

1.Create a working directory in your local system

	```mkdir opsmx-isd  #create a working directory for your installation```

2.Clone standard-gitops-repo repository from appropriate branch

	`git clone https://github.com/OpsMx/standard-gitops-repo.git -b 3.10`

3.Clone your gitops repo using the following command:

```
git clone https://github.com/<your-id>/<gitops-repo> 
For Example: git clone https://github.com/ravigorremuchu/oes-repo.git
```

4.Copy files from standard-gitops-repo to your gitops repo
```
cp -R ./standard-gitops-repo/config ./<gitops-repo>/config      
cp -R ./standard-gitops-repo/halyard.yaml ./<oes-repo>/halyard.yaml
cp -R ./standard-gitops-repo/default ./<oes-repo>/default
cp -R ./standard-gitops-repo/SAMPLES ./<oes-repo>/SAMPLES
```
5.Push the changes back to the githup repo
```
cd  <oes-repo> 
git add -A
git commit -m “Opsmx standard gitops repo”
git push
```
