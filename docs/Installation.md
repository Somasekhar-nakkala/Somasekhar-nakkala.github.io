#**Installation**#

##**ISD installation in a Kubernetes Cluster**##

This page describes the steps to install ISD in a Kubernetes Cluster.

###**Assumptions**###

1. Ability to install application on a Kubernetes Cluster

2. Internet access from the Kubernetes node

3. Access to Quay and Docker repositories is available


###**Pre-requisites**###

**Hardware requirements:**

* You should have access to a Kubernetes cluster with at least **2 nodes**
* Each node having a minimum of **8 CPU & 32 GB RAM**

**Other Pre-requisites**

You should have internet access and should be able to access github.com, docker.io and quay.io. You should have the following tools installed.

1. Choco package manager

2. Curl

3. kubectl-cli

4. kubectl-helm

5. git

6. login to "www.github.com"


**If you are using Windows machine**

Execute the following command in Powershell (running in administrator mode)

```
Set-ExecutionPolicy Bypass -Scope Process -Force; `iex ((New-Object System.Net.WebClient).DownloadString('http://chocolatey.org/install.psl')) 
```

Execute the following command:

* Install 'Curl' using the following command
 
	```$choco install curl```

* Install 'Kubernetes' using the following command 

	```$choco install kubernetes-cli```

* Install 'Helm' using the following command

	```$choco install kubernetes-helm```

* Install 'git' using the following command

	```$choco install git```


**If you are using Linux machine**
 
* Install-using-native-package-management, following the instructions provided here,
```https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/```

* Install helm using the following command & instructions provided here, 
```https://helm.sh/docs/intro/install/```and follow the instruction provided below

* Install 'git' using the following command
```$choco install git```


###**Pre-installation steps**###

1. Ensure that the kubeconfig file is named as "config" and is copied to .kube folder in your machine

2. You should have the NGINX Ingress Controller already available in the cluster; if not, install the same using the following instructions.

	* ```$kubectl create ns ingress-nginx```
		
		Note: It is the controller for service-to-cloud

	* ```$helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx```
		Note: Use "$helm repo" list to check ingress-nginx is added or not

	* ```$helm repo update```

	* ```$helm install ingress-nginx ingress-nginx/ingress-nginx -n ingress-nginx```
		Note: Installs nginx control in our namespace

	* ```$kubectl get svc -n ingress-nginx```
		
		Note: This command is used to check whether it is installed correctly or not

3. Check whether 'cert-manager' already installed in the cluster or not by running ```$kubectl get pods --namespace cert-manager```

4. If not installed, use the following commands to install the 'cert-manager'

	* ```$kubectl create namespace cert-manager```

	* ```$helm repo add jetstack https://charts.jetstack.io```

	* ```$helm repo update```

	* ```$helm install cert-manager jetstack/cert-manager --set installCRDs=true -n cert-manager```

		Note: This command installs cert-manager.


###**Installation Steps**###

1. Create a working directory in your local system
 
	```$mkdir <working-dir>```

	Note: Create a working directory for your installation

2. Clone 'standard-gitops-repo' repository from appropriate branch

	```$git clone https://github.com/OpsMx/standard-gitops-repo.git -b <branch-id>```

3. Login to GitHub (github.com) with your own credentials. If you don’t have an account, create an account in github.

	* Create a new private repository <oes-repo> with just README file. Make sure it is marked private.

	* Rename main branch as master branch. Go to settings/branches to rename the branch.

	* Get a git user token. Save this token to be updated in "values.yaml" later.
		
		Note: If the token contains ‘/’ or ‘\’, please generate another token as these special characters create some issues during installation.

4. Clone the newly created repo using the following commands

	```$git clone htts://github.com/<your-id>/<oes-repo```

	Example: git clone https://github.com/ravigorremuchu/oes-repo.git

5. Copy files (config, halyard and default) from standard-gitops-repo to the newly cloned repo directory

	* ```$cp -R .\standard-gitops-repo\config .\oes-repo\config```
		
		Note: Copy config file from standard-gitops-repo

	* ```$cp -R .\standard-gitops-repo\halyard.yaml .\oes-repo\halyard.yaml```
		
		Note: Copy halyard.yaml from standard-gitops-repo

	* ```$cp -R .\standard-gitops-repo\default .\oes-repo\default```
		
		Note: Copy config file from standard-gitops-repo

6. Go to your new git directory and execute the following commands:

	* ```$git add -A```

	* ```$git commit -m "some message”```

	* ```$git push```

7. To copy sample pipelines (go to your working dir and execute the following commands):

	* ```$git clone https://github.com/OpsMx/sample-pipelines.git```  
		Note: For getting pipelines sync with git

	* ```$cp -R .\sample-pipelines\pipeline-jsonfile\ .\<oes-repo>\pipeline-jsonfile\```
		
		Note: Copy pipeline-jsonfile directory into your git dir

8. Go to your git directory:

	* ```$git add .\pipeline-jsonfile\```

	* ```$git commit -m "pipeline promotion files"```

	* ```$git push```

9. Get "values.yaml" file from the Enterprise Spinnaker repo in the Git (https://github.com/OpsMx/enterprise-spinnaker/), from the appropriate branch and charts/oes directory and copy to your working directory.

10. Modify "values.yaml"

	* Under Image credentials make sure you have the right parameters defined:

		```imageCredentials:```

  		```registry: https://quay.io/```

  		```username:```

  		```Password:```

	* For spinDeck, spinGate, oesUI and oesGate: change the URLs to be used for accessing the OES/Spinnaker

		```Eg:```
  
		```host: spin.oes.opsmx.net```

		```host: spin-gate.oes.opsmx.net```

		```host: oes.oes.opsmx.net```

		```host: oes-gate-ldap.oes.opsmx.net```


	* Set "OpenLdap" to "true" & "installOpenLdap: true"

	* Go to gitopsHalyard section and make the following changes

  		```repo:```

     	```type: git``` 

     	```baseUrlHostName: github.com```

     	```organization: your-git-org```

     	```projectName: " "  <<Should be empty>>```

     	```repository: <your cloned repo name> <<Example: oes-repo>>```

     	```dynamicAccRepository: <your cloned repo name> <<Example: oes-repo>>```

     	```username:  your-git-id```

     	```token: your-git-token <<Access token corresponding to above login-id>>```



	* At pipeline promotion section, make this change:

		```pipelinePromotion: <<GitHub only not supported on S3 or Stash>>```

		```enabled: true```

11. Check if helm chart for opsmx is existing or not

	```$helm repo list```

12. Check if https://helmcharts.opsmx.com/ is existing. If existing, don't need to add the helm chart.  Otherwise, add using
	
	```$helm repo add opsmx https://helmcharts.opsmx.com/```

13. ```$helm repo update```

14. ```$kubectl create namespace <oes-ns>```

	Note: Create a namespace

15. Go to the directory where "values.yaml" is saved/modified and begin the installation using the following command. Note that this will install with the latest helm version.
	
	```$helm install oes39 opsmx/oes -f values.yaml -n <oes-ns> --timeout 30m```

	Note: "oes39" is the release name, "oes-ns" is the namespace. 

	If you need to use any specific version of helm, use the following command:
 
	```$helm install oes39 opsmx/oes -f values.yaml -n oes-ns --timeout 30m --version <helm-ver>```

16. Keep monitoring the pods being created using

	* ```$kubectl get po -n <oes-ns>```

		Note: -w for looping

	* ```$kubectl get sts -n <oes-ns>```

17. Check whether all the pods are up and running

18. Run the following command to get the URLs

	```$kubectl get ingress -n <oes-ns>```

19. Send the URLs and the external address to the DNS administrator to be added to the DNS server.





 
 









 

