#**Environment setup for OpsMx ISD**#

##**Environment setup**##

###**Pre-requisites**###
You should have internet access and should be able to access github.com, docker.io, 
and quay.io. The following tools should be installed on your system.

* curl 

* git 

* kubectl-cli
 
* kubectl-helm
 
* choco package manager (only for windows)

In addition, you need to create a Github repository.

###**Setup Laptop/machine used for ISD installation**###

Please follow the instructions that are specific to your laptop/machine operating system.

**Mac:**

* **curl, git** : Mac comes preinstalled with these commands

* **kubectl**: Install using instructions [here](https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/), 
using homebrew is generally easier

* **Helm**: Install using instructions [here](https://helm.sh/docs/intro/install/), using homebrew is generally easier


**Windows:**

* Execute the following command in Powershell (running in administrator mode)
```
Set-ExecutionPolicy Bypass -Scope Process -Force; `iex 
((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```
* **curl**: Execute this command at the power shell prompt: ```choco install curl```

* **git**: Execute this command at the power shell prompt: ```choco install git```

* **kubectl**: Execute this command at the power shell prompt: ```choco install Kubernetes-cli```
 
* **helm**: Execute this command at the power shell prompt: ```choco install kubernetes-helm```


**Ubuntu/Linux:**

* **Curl**: install using instructions [here](https://linuxize.com/post/how-to-install-and-use-curl-on-ubuntu-20-04/)

* **git**: Install using instructions [here](https://www.digitalocean.com/community/tutorials/how-to-install-git-on-ubuntu-20-04)

* **kubectl**: Install using instructions [here](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/), 
go with “using native package manager” if you are not sure.

* **Helm**: Install helm using the instructions [here](https://helm.sh/docs/intro/install/), 
using a package manager is generally easier

**Verification:**

Execute the following commands to verify that the commands are functional:

```
curl –version 
git –version 
kubectl version 
helm version
```

###**Creating a GitHub repo ("< **gitops-repo** >")**###

Github.com offers a free personal account. If you don't already have an account, 
follow the instructions [here](https://docs.github.com/en/get-started/signing-up-for-github/signing-up-for-a-new-github-account) 
to create a new account.

Login to GitHub (github.com) with your own credentials. 

* Create a new private repository, instructions are [here](https://docs.github.com/en/get-started/quickstart/create-a-repo). 
While creating:

	* Choose Private

	* Check “Add a README file”

	* Rename “main” branch as “master” branch by following the instructions [here](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-branches-in-your-repository/renaming-a-branch).

* Generate a **personal access token** by following the instructions [here](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token). 
Save this token to be updated in values.yaml later.

!!! Note
    If the token contains ‘/’ or ‘\’, please generate another token as these special characters may create an issue during installation.


###**Installing nginx ingress controller**###
You can skip this section if you are using another ingress controller, such as one provided by the cloud provider.

* ```kubectl create ns ingress-nginx``` 
* ```helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx``` 
* ```helm repo update``` 
* ```helm install ingress-nginx ingress-nginx/ingress-nginx -n ingress-nginx``` 
* Check whether it is installed correctly or not, use ```$kubectl get svc -n ingress-nginx``` 
* **Note down the IP Address (or hostname)** of the “ingress-nginx-controller” service in the output of the command above. 
This is required for making DNS or host entries as mentioned in the section below.


###**Installing cert-manager**###

You can skip this section if you're creating your own TLS certificates or the cluster doesn't have inbound port 80 access.

* ```kubectl create namespace cert-manager``` 
* ```helm repo add jetstack https://charts.jetstack.io``` 
* ```helm repo update``` 
* ```helm install cert-manager jetstack/cert-manager --set installCRDs=true -n cert-manager```


###**Adding entries to “hosts” file**###

Using DNS to map url-hostnames to IP addresses is the preferred method. However, in case you don’t have access to a DNS server, 
for trial purposes, we can access the ISD by manually adding the IP->host mapping in the “hosts” file as follows:


**Mac/Ubuntu/Linux**

section provides instructions for modifying your hosts file

1. Please follow the instructions [here](https://docs.rackspace.com/support/how-to/modify-your-hosts-file/).
2. Please create 3 entries as follows. The IP address is the “ingress-nginx-controller” service external IP 
address (as mentioned in the Nginx section above) and map them to the hostnames you defined for ISD.
	* Ip-address oes.com
 
	* Ip-address oes-gate.com
 
	* Ip-address spin...com [Example: oes.opsmx-isd.opsmx.com]

3. **If you skipped the step in defining host-names**, create these entries, replacing the “ip-address” as explained above:

	* Ip-address oes.isd-pov.example.com
 
	* Ip-address oes-gate.isd-pov.example.com
 
	* Ip-address spin.isd-pov.example.com


**Windows**

If you do NOT have DNS, add lines in hosts file as shown below, by following the 
instructions [here](https://docs.rackspace.com/support/how-to/modify-your-hosts-file/):

* Please create add these three lines replacing the “ip-address” with the **IP-address 
is the “ingress-nginx-controller” service external IP address**

	* Ip-address oes.isd-pov.example.com 

	* Ip-address oes-gate.isd-pov.example.com 

	* Ip-address spin.isd-pov.example.com [Example: 35.22.105.22 oes.isd-pov.example.com] 

**If using DNS**, add the entries in host file as shown below, by following the 
instructions [here](https://docs.rackspace.com/support/how-to/modify-your-hosts-file/):

* Please create 3 entries as follows. The IP-address is the “ingress-nginx-controller” service 
external IP address (as mentioned in the Nginx section above) and map them to the hostnames you defined for ISD.

	* Ip-address oes.com 

	* Ip-address oes-gate.com 

	* Ip-address spin.com [Example: 35.22.105.22 oes.opsmx-isd.opsmx.com] 





 
 









 

