#**ISD Installation Guide**#

##**Installing ISD on a kubernetes cluster**##

This document provides the step-by-step instructions for installing ISD on a kubernetes cluster. 
Some basic knowledge of command-line interface is required.

Before you start, it might be helpful to go through these documents: 

* Routing Web URLs to ISD services - Refer [here](https://docs.opsmx.com/products/opsmx-intelligent-software-delivery-platform/opsmx-isd-installation/routing-web-urls-to-isd-services)
 
* ISD On-Prem POV Infrastructure requirements - Refer [here](https://docs.opsmx.com/products/opsmx-intelligent-software-delivery-platform/opsmx-isd-installation/isd-on-prem-pov-infrastructure-requirements)
 
* ISD - Commonly used Commands - Refer [here](https://docs.opsmx.com/products/opsmx-intelligent-software-delivery-platform/opsmx-isd-installation/isd-commonly-used-commands)
 
* ISD Service Catalogue - Refer [here](https://docs.opsmx.com/products/opsmx-intelligent-software-delivery-platform/opsmx-isd-installation/isd-service-catalogue) 


###**Pre-requisites**###

* A laptop/machine being used to install that has the required software mentioned here

* A **kubeconfig** file to access the kubernetes cluster

* A working “kubectl” command. Execute the command below:

	* ```kubectl get no ```# to see the nodes
	* ```kubectl get ns ```# to see the namespaces

* These commands should show some output. If required, rename the **kubeconfig** file as "**config**" and copy to .kube folder in your machine

* A github repo, and a “personal access token”. Instructions for creating these can be found here.

* NGINX Ingress Controller and cert-manager(if using https) in the cluster. If not already installed, install by using the instructions here

* Access to a DNS server. If you do not have access to a DNS server, you can add the host-names to “hosts” file on your machine by following the instructions here


###**Pre-installation steps**###

1. Decide on the host names that will be used to access ISD

2. Clone the standard-git-ops repository and copy the contents to your github repository
 
3. Select a “values.yaml” file from the SAMPLES/values-yamls folder in your github repository, and edit the contents
 
4. Prepare helm for installation
 
5. Install ISD using the helm command
 
6. Login to the ISD instance created


###**Installation steps**###

**Setup URLs**

1. Host names that will be used to access ISD: Three host-names are required as mentioned below:

	* oes.< subdomain>.company.com

	* spin.< subdomain>.company.com

	* oes-gate.< subdomain>.company.com

 	For Example: oes.isd-from-opsmx.opsmx.com

2. Run the following command to get the IP address of the ingress controller

	```kubectl get ingress -n ingress-nginx```

3. If you have access to a DNS server, add the three host names from step 1 to the DNS server pointing them to 
the IP Address in step 2 above. If you do not have access to a DNS server, follow the instructions here to add 
them locally to your laptop.


**Prepare gitops repo**

Prepare your gitops repository as follows:

1. Create a working directory in your local system

	```
	mkdir opsmx-isd  #create a working directory for your installation
	```

2. Clone standard-gitops-repo repository from appropriate branch

	```
	git clone https://github.com/OpsMx/standard-gitops-repo.git -b 3.10
	```
3. Clone your gitops repo using the following command:

	```
	git clone https://github.com/<your-id>/<gitops-repo> 
	For Example: git clone https://github.com/ravigorremuchu/oes-repo.git
	```
4. Copy files from standard-gitops-repo to your gitops repo

	```
	cp -R ./standard-gitops-repo/config ./<gitops-repo>/config      
	cp -R ./standard-gitops-repo/halyard.yaml ./<oes-repo>/halyard.yaml
	cp -R ./standard-gitops-repo/default ./<oes-repo>/default
	cp -R ./standard-gitops-repo/SAMPLES ./<oes-repo>/SAMPLES
	```
5. Push the changes back to the githup repo

	```
	cd  <oes-repo> 
	git add -A
	git commit -m “Opsmx standard gitops repo”
	git push
	```

**Prepare values file**

1. ```cd  ./<gitops-repo>/SAMPLES/values-yamls``` and select a template values file. Start with “```easy-values.yaml```” for a very simply insecure installation.

2. Edit the **value.yaml** file selected as follows:

	* For spinDeck, spinGate, oesUI and oesGate: change the hostnames decided in step 1, which will be used for accessing the OES/Spinnaker
		
		For Example:

		host: spin.oes.opsmx.net host:
 
		oes.oes.opsmx.net host: 

		oes-gate-ldap.oes.opsmx.net


	* Go to **gitopsHalyard** section and update gitops repo information:
		
		```
		repo:
 		 type: git 
		 baseUrlHostName: github.com
		 organization: <org> # e.g “srini” in https://github.com/srini/isd-repo
		 repository: <your cloned repo name> #Eg: isd-repo
		 dynamicAccRepository: <your cloned repo name> #Eg: isd-repo
		 username:  your-git-id  # https://github.com/srini/isd-repo
		 token: your-git-token  # Access token corresponding to above login-id
		```

**Install ISD**

1. Add helm repo and create namespace

	```
	helm repo list
	helm repo add opsmx https://helmcharts.opsmx.com/
	helm repo update
	kubectl create namespace opsmx-isd #[create a namespace]
	```

2. Navigate to the directory where values.yaml is saved/modified and begin the installation. This may take up to 30 min depending on the network speed.

	```
	helm upgrade –install isd opsmx/oes -f easy-values.yaml -n opsmx-isd --timeout 30m
	```

3. Keep monitoring the pods being created using

	```
	kubectl get po -n opsmx-isd  -w
	```

4. Once all the pods show 1/1 or 2/2 or “0/1 completed” status, open your browser and navigate to “oes..company.com”, the host name selected in step 1 

5. Login with user name ‘admin’ and password as “opsmxadmin123” 

6. Click on “**set-up**” on the bottom right corner, and proceed to “cloud accounts” 

7. Create a kubernetes account, and name it as “**default**”


**Common Issues during helm installation**

Most common issues during installation are related to incorrect values in *-values.yaml. Should you realize that there is a mistake, it is easy to correct it.

* Update the easy-values.yaml (or which ever file name you are using)

* Wait for the helm install to error out, it is best to not break the process

* Simply re-execute the “helm upgrade –install …” command given:

	```
	helm upgrade –install isd opsmx/oes -f easy-values.yaml -n opsmx-isd --timeout 30m
	```

Should this not work, please follow the instructions for “Cleaning up” below and start-over.

**Cleaning up**

Use the following commands to delete the entire installation from kubernetes

** Option 1**

Issue this command, replace -n option with the namespace

```
helm uninstall isd -n opsmx-isd
```

**Option 2**

Issue these commands, replace -n option with the namespace

```
kubectl -n opsmx-isd delete deploy --all
kubectl -n opsmx-isd delete sts --all
kubectl -n opsmx-isd delete svc --all
kubectl -n opsmx-isd delete ing --all
kubectl -n opsmx-isd delete cm --all
kubectl -n opsmx-isd delete secrets --all
kubectl -n opsmx-isd delete role --all
kubectl -n opsmx-isd delete rolebinding --all
kubectl delete ns opsmx-isd
```





 
 









 

