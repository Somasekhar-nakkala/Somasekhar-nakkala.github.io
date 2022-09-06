#**Helm Chart based installation**#

##**Helm Chart based installation - Detailed**##
The document is primarily intended to be used for Helm based ISD installation. 
Follow these steps if you are trying to install ISD yourself.

###**Pre-requisites**###

To use this document you would need prior familiarity of Helm chart concepts, 
YAML, Kubernetes knowledge and working knowledge of GitHub & Spinnaker tool.

###**Chart Basics**###

ISD helm chart is located at https://github.com/OpsMx/enterprise-spinnaker.git. 
We fully support installations with custom-CAs and/or airgapped installations.

!!! Note
  	Various versions of the chart are available in separate branches of the same repo. 

For installation, you need the following:

1. Standard-gitops repo: [https://github.com/OpsMx/standard-gitops-repo.git](https://github.com/OpsMx/standard-gitops-repo.git) . Select the “branch” in this repo as appropriate 

	* AIRGAPPED: [https://github.com/OpsMx/airgap-gitops.git](https://github.com/OpsMx/airgap-gitops.git)

2. Sample pipelines: [https://github.com/OpsMx/sample-pipelines.git](https://github.com/OpsMx/sample-pipelines.git)

3. Plugins: [https://github.com/OpsMx/datasource-plugins.git](https://github.com/OpsMx/datasource-plugins.git) and [https://github.com/OpsMx/spinnakerPluginRepository.git](https://github.com/OpsMx/datasource-plugins.git)

	* AIRGAPPED: See additional configuration information for oes-gate and spin-orca

###**Chart Contents - OES**###

It is best to clone this “opsmx/enterprise-spinnaker.git” repo and familiarize yourself with the contents before proceeding further.

In the "enterprise-spinnaker/chars/oes" folder, these are the key folders:

1. Values.yaml: This is the base values.yaml file that contains all the defaults for the chart. 
All values here can be overridden by the "-f values-changed.yaml" option on the helm install command. Only the changed values need to be present in this file.

2. Templates: This folder contains all the OES components and their configurations

3. Charts: This contains charts that we package along with OES. These include Spinnaker, minio, redis, prometheus, 
elastic & more. Key changes are in the Spinnaker chart. Key files inside the "spinnaker-chart" are:

	* Templates: These are the Spinnaker related templates

	* Config: These are files that are included in the "templates-files"

4. Configs: This contains files (yaml's, scripts) that are included in the OES templates

###**Chart Options**###

###**Routing UI to backend**###

Routing traffic from web-browser (UI) to various services in the backend can be done in multiple ways 
depending on the environment. This includes ingress, cert-manager or custom certs (for TLS, also known as https), 
load balancers, node-ports, etc. To keep it simple, all we need to know at this point in time is that 
all UI traffic is routed via “gate”, a spinnaker component.

###**Gate**###

SAPOR service in OES talks to Spinnaker services. All the communication OES makes with Spinnaker is done thru 
SAPOR service and it does so via “gate” (or multiple gates, a gate is a spinnaker component used by the Spinnaker 
UI and all API callers communicate with Spinnaker). As a design choice, OES does not communicate directly with any of 
the spinnaker services, though front50 and echo do have outbound communication to OES SAPOR.

These are the options:

1. OES-Gate: This is the default option. All communication from the UI (OES UI & spin-deck also known as Spinnaker Gate) and the communication between SAPOR and Spinnaker happens via this ‘common’ gate. 

	```Values.yaml: global.commonGate.enabled=true```

	Files: ```templates/(deployment|configmaps)/oes-gate*.yml```

2. OES-Gate: For OES components and “spin-gate” for spinnaker components.

	```Values.yaml: global.commonGate.enabled=false``` (Note: This option is usually not used)

3. SAPOR-Gate:  This is an additional gate into Spinnaker that is configured with basic authentication i.e. simple username and password. As SAPOR connects to spinnaker services via OES-Gate (or spin-gate), the API needs to be authenticated. SAPOR-Gate is an option when 2-factor authentication is enabled on OES-Gate or spinnaker-gate.
	
	Note: SAPOR-Spinnaker authentication is not possible using UI.
	
	```Values.yaml:  saporGate.enabled=false``` (Disabled by default)
	
	```saporGate.config.username, password : basic auth credentials```
	
	Files: ```template/sapor-gate folder contains the required files```

4. X509: This is not really a gate but an option to authenticate to spin-gate via x509 certificate authentication. This requires that the TLS termination happens at the gate and not at the ingress or load balancer. 

5. Values.yaml: The configuration is part of spinnaker.gitOps and is involved. Best to configure manually as certificates need to be generated and imported into secret stores.

Normally, one would not need to touch the “oes-gate-config” configMap. However, it is good to know certain key points:

* services.fiat.enabled: true (Ensure that this is set to true of Spinnaker authZ (fiat) is enabled 

* service.gate and deck: These should point to external oes-gate and spin-deck URLs

* spinnaker.extensibility.repositories: URL mentioned is accessed for downloading plugins related for Visibility, Verification and Policy. If they are not working (showing as JSON) in Spinnaker UI, this is a place to check.


###**Autoconfig**###

OES needs to configure the Spinnaker connection. In addition, even though Autopilot and OPA are installed along with the chart, these are separate data sources and need to be configured in the “integrations” page of the OES set-up.

```Values.yaml: oesAutoConfiguration: true``` (Default is true)

Files: ``` templates/hooks/oes-config-job.yaml templates/configmap/datasource-creation.yaml```

This job runs a script in the “datasource-creation” configMap that:

* Waits for all pods to be up

* Makes the curl calls to add gitops, autopilot and OPA data sources

* Makes a curl call to add Spinnaker

!!! Note
   	Check the logs of “oes-config-*” pod, if these are not auto-configured.


###**OpenLDAP**###

Both for authentication and authorization, OES and Spinnaker can be configured to use any third party authentication service like LDAP, OpenLDAP, OAuth etc. OES helm package includes OpenLDAP that contains the following users and groups:

*ID: admin / Password: opsmxadmin123 (Belongs to all groups)*

*ID: user1 / Password: user1password (Belongs to “developers” group)*

*ID: user2 / Password: user2password (Belongs to “qa” group)*

*ID: user3 / Password: user3password (Belongs to both developers and qa groups)*

```Values.yaml: global.installOpenldap: true```

Files: charts/openldap/templates

!!! Note
   	* Default settings in “standard-gitops” repo are for authentication enabled but authorization is disabled. If you want to enable, please do check ‘fiat’ param in the “oes-gate” config as well.

	* “global.ldap.enabled=true” is independent of openLDAP. That means, the users are free to use other LDAP.


###**Open Policy Agent (OPA)**###

OES supports enforcing “policy”. A policy is an execution condition, a simple policy might be “don’t allow deployment on weekends”. There are two kinds of policies: Static and Runtime. 
This is how it works in OES. 

**Static Policy**

front50 has a web-hook configured that is called before a pipeline is saved. 
The configuration is in “.hal/defaults/profile/front50-local.yml”. You can see it in the standard-gitops repo “default/profiles/front50-local.yml”, policy.opa.enabled and URL that points to “OES-Sapor”.

!!! Note
   	* It is always possible to disable the policy enforcement using policy.opa,enabled: false.

	* If OES is installed in a different namespace or a different cluster, this URL needs to be updated to route it correctly to OES-Sapor.

	* OpsMx front50 image is required for this functionality, hence this feature doesn’t work with Open Source Spinnaker


**Runtime Policy**

Runtime policy is enforced by adding a “policy” stage in the pipeline through the OES-UI

**Execution**

In both these cases, an API call comes to SAPOR which in turns makes a call the configured OPA server. For providing this functionality, an OPA server is required. An OPA server is installed by default in the chart, which installing OES.

```Values.yaml: opa.enabled: true``` (Enabled by default)

Files: ```templates/deployments/opa-deployment.yaml and templates/configmaps/opa-persists,yaml```

**Note**: OPA server is required and expected by the install. If required, it can be disabled later. Customer’s OPA server, if any, can be configured in addition to this OPA-server.


###**Create-controller-secrets (Agent Configuration)**###
  
OES supports Spinnaker deployment via an “Agent” that is installed in a remote cluster with no inbound network access (of course, outbound is required). 

Controller and Agent talk to each other and everyone through custom self-signed certificates that need to be made available to all parties. To this end, a custom job “create-controller-secrets” is executed at the start of the installation. It only needs to be executed once (unless the created secrets are deleted). It creates the following: 

1. **ca-secret:** This is the self-signed CA and its key. This is mounted as a volume in the controller so that it can generate all the required certificates.

2. **oes-control-secret:** This contains the CA and x509 certs required for SAPOR to talk to the controller. From OES-UI, when we add an agent, SAPOR talks to the controller to generate the required configuration content.

3. **Jwt-secret:** Used by the controller

4. **oes-cacerts:** This contains the JAVA key-store “cacerts” that has the self-signed CA added to it. This is mounted on Igor (for remote jenkins), etc. It can be mounted in any of the pods that need to access any extra certificates.

The “create-controller-secrets” custom job allows for the following (both being optional):

* Provide your own base “cacerts”: If a customer has 10-20 CAs that need to be added, we could use java ‘key-tool’ to add all of them and provide that “cacerts” as the base to which the controller CA is added.

* Provide your CAs: If a customer wants their own CA to be added, this can optionally be provided. It is added into the base “cacerts” that are already baked into the image.

```values.yaml: forwarder.enabled: true``` (If false, remove mounts in default/service-settings.)
Files: ```templates/forwarder```

!!! Note
   	* It is OK to rerun this job by deleting all the secrets created by it. This may be required, for example, to add another CA. However, ensure that SAPOR controller and any pods mounting “oes-cacerts” are restarted. All agent manifests should be deleted, re-created via the OES-UI and re-applied in the remote clusters.

	* Only to rename the “controller-agent-grpc” endpoint, rerunning this job is NOT required. Simply update the controller configMap and restart the controller. The controller creates its own certs based on the “ca-secret” contents. As it recreates it every time on start-up, running this job is not required. Do download the agent manifests afresh. 


###**Chart-Custom CA**###

OES Helm chart fully supports the use of a custom CA (Certificate Authority). This is done in two parts: 

1. The “create-controller-secrets” job allows you to include a CA in “oes-cacerts” secret. Ensure that custom CA is part of “oes-cacerts”.

2. This secret is mounted in all the pods, both in OES and Spinnaker. In OES it is mounted by specifying the flag in “*values.yaml*” and in Spinnaker is mounted by adding entries to “*.hal/default/service-settings/*.yml*” files

	```values.yaml: global.customCerts.enabled: true``` (Default is false)
	```global.customCerts.secretName``` (DO NOT CHANGE)
	Files: ```standard-gitops repo, default/service-settings/*.yml.```

	!!! Note
   		To test this, access the resources and look for SSL exceptions in pod-logs.

###**Chart-Airgapped Install**###

OES Helm chart fully supports installing in an airgapped environment. This is done in the following way:

1. Obtain all docker images required and upload to local image repository 

2. In “values.yaml”, update global.customImage.registry as appropriate 

3. Use the “opsmx/airgap-repo” instead of the “standard-gitops” repo. 

!!! Note
  	* The repo contains “.boms” folder that may not be listed with ls command.

	* Images repo locations need to be updated as appropriate

In addition, OES uses “plugin-framework” for adding functionality to Spinnaker. Spinnaker natively supports PF4J that requires downloading files from an unauthenticated git repository. If an unauthenticated git repository is available within the airgapped environment:

* Update “*spinnaker.extensibility.repositories.url*” in the “oes-gate” configmap to point to a repo that is reachable by Spinnaker. Copy the contents from the opsmx repo into the local repo.

* Update the same parameter in “*orca-local.yml*” (in .hal/defaults/profile)

###**Halyard GitOps**###

This section describes the basics of how the gitOps works and the various players in the process.
 
There is a repo (github repo for this document, but could be S3, bitbucket, etc), typically copied from “*github.com/opsmx/standard-gitops repo*” This repo contains files that are used by Spinnaker Halyard. All the contents of “*/home/spinnaker/.hal*” inside the halyard pod are placed there by pulling them from the repo and editing them to replace certain values.

OES updates the spinnaker configuration by writing to this repo. Spinnaker configuration is updated by restarting the halyard pod.

This process is enabled by multiple “*init*” containers that run before the main halyard container.

```values.yaml: spinnaker.gitOpsHalyard.*```

```Spinnaker.spinCli.*```

Files: ```charts/spinnaker/templates & charts/spinnaker/config```


###**Container: create-halyard-local**###

This is the first container that executes the “init.sh” script that is located in the "*-opsmx-spinnaker-halyard-init-script” configMap. Note that the script gets modified based on the repo-type during the installation process. 
Here is a high-level description of the script:

1. Clone the “git-repo”

2. Replace “SPINNAKER_NAMESPACE” string in “.hal/config, default/profiles/fiat-local.yml”, “orca-local.yml” (custom jobs)

3. Replace “RELEASE_NAME” string in “.hal/config, redis.yml” in “default/service-settings”

4. Replaces “GIT_TOKEN”, “GIT_USER” and “DYNAMIC_ACCNT_CONFIG” in “default/profiles/spinnakerconfig.yml”


###**Container: halyardconfig-update**###

As we use git-repo for halyard configuration, secrets used by halyard (for example, account passwords) could be exposed to anyone who has git-repo access. 

OES chart supports using Kubernetes secrets in halyard configuration. The procedure is as follows: 

1. Manually create a Kubernetes secret with a key and password in the spinnaker namespace. Create it as:
	
	```K create secret generic  --from-literal =MYSECRET```

2. In the “*git-repo*”, refer to it as ```encrypted::``` in the files where it is used

3. If need to encrypt a File, create the secret and refer to it as ```encryptedFile::``` Create it as, ```k create secret generic SECRETNAME --from-file```

This init container runs the “run.sh” script in -opsmx-spinnaker-spin-secret-decoder configMap that searches and replaces all values as appropriate.


###**Container: halyard-overrideurl**###

OES requires 3 (max 5 including controller) end-points that need to be reachable from the web-browser. These are:

* oes-ui

* oes-gate

* Spin-deck

* spin-gate (only if NOT using the common-gate, usually not required)

* "agent-grpc" service of the controller (if configuring agent/controller)

Routing Web-URLs to OES and Spinnaker pods requires multiple configurations depending on the following URLs type:

1. Ingress: In this model there is one loadbalancer/IP that routes traffic to an ingress controller (e.g. nginx). The ingress controller looks at the URL string (example, spin.saas.opsmx.com or oes.saas.opsmx.com) and based on this string routes the traffic to the appropriate pod. Note that for this to work, DNS (example, www.godaddy.com) is a critical requirement as IP address needs to be translated to URLs.

2. Load Balancers: Most cloud providers have automatic load balancer provisioning. This allows for any service to have an external IP and traffic is directly routed to the appropriate service or pod. In addition, we can still use the DNS to reach the LB.

3. Node Port: In case load balancer is not available, Kubernetes allows IP address of any of the worker nodes to be used with a “node port” (instead of the service-port) to route the traffic to pods


**HTTP or HTTPS**

If using HTTPS, additional complexity comes in the form TLS certificates and termination.

1. **Ingress:** In most cases, ingress will allow for TLS termination. Coupled with cert-manager+ACME, automatic certificates can be generated. Default OES installation assumes “nginx” and “cert-manager”.

	* If using custom-certificates, these need to be generated and provided to ingress controller for TLS termination. This is easily done by create the TLS secrets mentioned in the default ingress objects created (kubectl get ing)

	* If using any other ingress (other than nginx), the ingress object configuration has to be changed accordingly.

2. **Load Balancers:** Most cloud providers provide options for TLS termination at the Load Balancer. If this option is chosen (makes sense only if DNS is in place), the certificates need to be loaded to the cloud provider for TLS termination.

3. **Node Port:** In this case, it is assumed that we are in development mode and HTTPS is not supported.

```
values.yaml: global.spinDeck.host, oesUI.host, oesGate.host, spinGate.host, protocol k8sServiceType: ClusterIP or LoadBalancer
```

This init container runs the “call_overrides.sh” script in *-opsmx-spinnaker-halyard-overrideurl configMap. This is designed to support 3 situations:

* Gate and deck use ingress and URLs are provided and gitops is in use. In this case, the hal/config overrideUrls are edited to appropriate values.

* No URLs are provided, load-balancer IP is used for figuring out the URLs for gate and deck.
 
* No URLs are provided and there is no external IP for the load-balancer after waiting for 5 minutes. In this case, Node IP with node-ports is used for configuring the gate and deck URLs.

###**Container: halyard**###

This is the main halyard container that runs the halyard executable. There is a postStart lifecycle hook that waits for the halyard process to be ready and then executes “*hal deploy apply*”.

!!! Note
   	* If you see any errors, check the logs of the “create-halyard-local container”. In most cases, it is unable to fetch the repo because the git-token may be incorrect or has expired.
 
	* If the halyard pod stays hung in “podInitializing” state for more than 5 minutes, it means that the halyard daemon process is unable to start. The issue here is most likely related to “halyard-local.yml” file in the “standard-gitops repo”. Easiest way to debug is to remove the “poststart” lifecycle hook and check the container logs, “exec” into the container and execute “hal-deploy-apply” manually.
 
	* If we see “decryption” errors in “hal-deploy-apply”, most likely the cause is the mismatch between SAPOR encryption and halyard decryption. SAPOR encrypt key can be found in bootstrap secret and oes-sapor-config CM. Halyard decrypt key is found in .hal/default/profiles/spinnakerconfig.yml


###**Install-using-hal**###

This is the primary installer in the Spinnaker helm-chart. This has been modified to add a container.

```values.yaml: spinnaker.spinCli.*```

Files: ```charts/spinnaker/templates/hooks/install-using-hal.yaml & charts/spinnaker/templates/secrets/spin-config.yaml```


###**sample-pipeline-install**###

This creates the following applications in Spinnaker,  

1. “Opsmx-gitops” application in spinnaker is needed for “Restart Spinnaker” in OES UI to work. It also contains the “syncToGit” and “syncToSpinnaker” pipeline promotion pipelines for backing up Spinnaker applications.

2. “sampleapp” application contains a few sample pipelines to demonstrate spinnaker functionality and also act as a quick reference.

This container (not an init container) executes the “spin-pipeline-import.sh” script that:

* Waits for all spinnaker services to be up.

* Clones the sample pipeline repo at “[https://github.com/OpsMx/sample-pipelines.git](https://github.com/OpsMx/sample-pipelines.git)”.

* Executes “spin-cli” commands to push sample pipelines into Spinnaker. For this it needs the gate-API url, username and password that is taken from a secret created from values.yaml, spinnaker.spinCli section.

!!! Note
   	* This step is mandatory. Opsmx-gitops application is needed for restart functionality to work.

	* If *sampleapp* is not there in spinnaker after install, check the logs of this container.