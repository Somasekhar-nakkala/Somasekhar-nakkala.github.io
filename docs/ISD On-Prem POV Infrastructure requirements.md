#**ISD On-Prem POV Infrastructure requirements**#

##**ISD On-Prem POV Infrastructure requirements**##

These requirements for setting up a Proof of Value are not sufficient for a production ready environment. 
Each customer’s environment is unique and an installation often involves tailoring ISD to each customer’s 
unique environment and constraints. By communicating key constraints of your environment upfront, An OpsMx 
Engineer can help you install ISD quickly and efficiently.

!!! Note
	We understand that not every user may be able to answer all of these questions and, 
	while we highly recommend contacting your infrastructure team(s), we also understand that not every user 
	has that option. If this is the case, feel free to contact us using [https://www.opsmx.com/contact/](https://www.opsmx.com/contact/) and we 
	will be happy to help you find the answers.

###**Requirements**###

* **Kubernetes Cluster**: Minimum of 24 CPU and 96 GB of RAM available. Each node should have a minimum of 32 GB. Typically 3 Nodes of 8 CPU/32 GB RAM should be enough. 
	* Automatic PVC provisioning is assumed 
	* Automatic LoadBalancer provisioning is assumed 
	* Most cloud-based k8s have these features. If you are using a non-standard k8s that requires manual provisioning, please let an OpsMx Engineer know.

* **Access**: Admin access to at least one namespace is required.

* **Network**: Full internet access, without http proxy will result in a simple installation process. 
Internet access via http-proxy, or a full air-gapped (no internet) connection is possible. Note that a 
gRPCconnections  are made to google servers even if using proxy-servers. Please let us know upfront if we 
know of any restrictions in internet access.

* **Security**: It is possible that security restrictions can interfere with the process of serving web-pages 
from inside a kubernetes cluster. Please let us know if you know of cloudflare or similar software that 
intercepts all traffic to internal web-sites.

* **Repository**: ISD requires a repository for storing its configuration. In other words, users will need a 
github, gitlab or bitbucket repository. Alternatively,  users can use AWS S3 bucket or install gitea, a small, 
open source, local github.

* **Custom CA certificates**: ISD integrates with multiple other applications in the environment. 
If custom CA certificates are in use in your environment, please inform an OpsMx engineer.  
It is required that  ISD ‘trusts’ your environment.

* **SSO or single-sign-on**: We recommend that initial installation and configuration be done with 
our install-time provided open-LDAP that provides an admin user, user1, user2 and user3 for testing 
RBAC (Role based Access control). Should you require that we integrate with your SSO as part of POV, 
please ensure that we have support of your infrastructure team that handles the SSO i.e. AD, Okta or 
any other. As SSO configuration requires multiple decisions and is dependent on the decisions made before, 
an element of trial-and-error is involved to “get it just right”.

* **Secrets**: During installation, we support the use of k8s secrets. Should you need any other secret 
manager “during installation”remember to ask an OpsMx engineer for help.

	!!! Note
		At no point do we request you to give your account credentials OR store it in a repo that is accessible to 
		OpsMx employees. Should that be requested, please report to the OpsMx security officer.

		We support multiple secret managers “during use” (as against during installation).

* If ISTIO or any other service mesh is in use, this may require additional considerations. 
Please contact an OpsMx Engineer for assistance.


###**Installation Process Requirements for Machines/Laptops**###

The machine/laptop used for installation has these requirements: 

* We should have internet access and should be able to access github.com, docker.io and quay.io and 
we need the following tools and services:

	* curl 

	* kubectl (kubernetes command line interface)
 
	* helm (package manager from https://www.hashicorp.com/) 

	* git 

	* Github user ID and login in github.com 

	* choco package manager (only if using Windows Laptop)






 
 









 

