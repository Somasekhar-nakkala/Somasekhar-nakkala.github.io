#**ISD Service Catalogue**#

##**Spinnaker is composed of a number of independent microservices**##

###**List of Spinnaker Services:**###

* **spinnaker-halyard**: is Spinnaker’s configuration service

* **spin-deck**: is the browser-based UI

* **spin-gate**: is the API gateway

* **spin-orca**: the orchestration engine for executing pipelines. It handles all ad-hoc operations and pipelines

* **spin-clouddriver**: responsible for all mutating calls to the cloud providers, docker and git-repos

* **spin-front50**: is used to persist the metadata of applications, pipelines, projects

* **spin-rosco**: The bakery that produces immutable VM images (or image templates) for various clouds.

* **spin-igor**: is used to trigger pipelines 

* **spin-echo**: is Spinnaker’s eventing bus for sending notifications

* **Fiat**: is Spinnaker’s authorization service. If RBAC is enabled, other services call Fiat to check if an operation is permitted or not


###**List of Autopilot Services:**###

* **Agent-grpc**: Used by agents to connect to the controller, used to communicate with controller

* **Oes-db**: Postgres database used to store the ISD data
 
* **Oes-autopilot**: For analysis of logs/metrics
 
* **Oes-dashboard**: Used to display the dashboard in ISD UI 

* **Oes-gate**: is the API gateway of ISD Oes-platform: Used for saving data integrators, cloud provider accounts etc. 

* **Oes-sapor**: Used for connecting to spinnaker and OPA 

* **Oes-ui**: Main UI of ISD 

* **Oes-visibility**: Used for approval/visibility gate 

* **Oes310-minio**: Used by spinnaker and data science for storing data. 

* **Oes310-openldap**: Used for open-ldap authentication
 
* **Oes310-redis-headless**: Not used 

* **Oes310-redis-master**: Used for caching purposes (gate, orca, clouddriver among others)
 
* **Oes310-spinnaker-halyard**: Used to connect to spinnaker (apply config changes etc)
 
* **Opa**: Is the policy engine 

* **Opsmx-controller-controller1**: Primary service to support agent communication with the rest of the services
 
* **opsmx-controller-controller1-interproc**: Not currently used 

* **Sapor-gate**: Used by oes-sapor to connect to a spinnaker services when 2FA or SAML is used.

* **Oes-audit-client**: Used to retrieve the audit data to display in the UI. Available in ISD 3.10 

* **Oes-audit-service**: Used to save the audit data. Available in ISD 3.10 

* **Oes-datascience**: Artificial Intelligence/Machine Learning Engine of logs & metrics. Available in ISD 3.10 

* **Rabbitmq-service**: Tuning service used in oes data science for asynchronous operations/analysis. Available in ISD 3.10


###**List of Jobs**###

* **Create-controller-secret**: This job creates ca-secret, oes-cacerts, jwt-secret and command-secret. These secrets and certs are used for Secure communication between agent and controller.

* **Oes-config**: This job attempts automatic configuration of Autopilot-Spinnaker communications at install time. If it fails, this needs to be done manually. 

* **oes310-create-sample-app**: Used to create sample applications from a git-repo in the spinnaker at the install time. If this fails, one can manually do it by following the instructions given [here](https://github.com/OpsMx/sample-pipelines/blob/main/create-sample-job.yaml). 

* **Oes310-install-using-hal**: This is part of the Spinnaker installation.

