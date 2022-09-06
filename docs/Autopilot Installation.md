#**Autopilot Installation**#

If you are already using Open Source Spinnaker, then Autopilot can get seamlessly integrated with your Open Source Spinnaker and you get all the benefits of Autopilot product. The following section defines the procedure for installing Autopilot as a standalone module to work with Open Source Spinnaker.

!!! Note
   	If you are installing Autopilot along with ISD, then refer the detailed installation procedure [here](https://docs.opsmx.com/products/opsmx-intelligent-software-delivery-platform/opsmx-isd-installation).

	Refer the below installation procedure **only if** you are installing Autopilot as an independent module and want to integrate it with Open Source Spinnaker 

##**Prerequisites**##

1. Have access to public repositories in docker.io and quay.io
 
2. Have the following tools installed - wget, kubectl, helm
 
3. Have access to a Kubernetes cluster with at least 2 nodes, each node having 8 CPU and 32 GB RAM
 
4. Have the NGINX Ingress Controller installed in the cluster. If it is not already installation, then you can install the same using the following instructions  

	```$ kubectl create ns ingress-nginx```
 
	```$ helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx```
 
	```$ helm repo update``` 

	```$ helm install ingress-nginx ingress-nginx/ingress-nginx -n ingress-nginx```
 
	```$ kubectl get svc -n ingress-nginx```

5. Have “cert-manager” already available in the cluster. If it is not already available, then you can install it using the following instructions

	```$ kubectl create namespace cert-manager```
 
	```$ helm repo add jetstack https://charts.jetstack.io```
 
	```$ helm repo update```
 
	```$ helm install cert-manager jetstack/cert-manager --set installCRDs=true -n cert-manager```

6. In the Kubernetes cluster, have 3 Persistent Volumes of size 10GB each

7. Two DNS records pointing to the IP of the Ingress Controller for the following:

	* “Autopilot UI”, Eg. autopilot.opsmx.net 

	* “Autopilot Gate”, Eg. autopilot-gate.opsmx.net


##**Autopilot Installation Steps**##

Following are the steps to install Autopilot.

1. Download yaml file from the Enterprise Spinnaker repository in the GitHub

	```$ wget [https://raw.githubusercontent.com/OpsMx/enterprise-spinnaker/oes3.10/charts/oes/values-APforOSS.yaml](https://raw.githubusercontent.com/OpsMx/enterprise-spinnaker/oes3.10/charts/oes/values-APforOSS.yaml)```

2. Update Autopilot UI and Autopilot Gate entries

	```#Autopilot UI URL configuration```

	```oesUI:```
 
  		```host: << AUTOPILOT UI URL Example autopilot.opsmx.net >>```

	```#Autopilot Gate URL configuration```

	```oesGate:``` 

  		```host: << AUTOPILOT GATE URL Example autopilot-gate.opsmx.net >>```

3. Update Spinnaker Deck URL in dashboard section of values-APforOSS.yaml

	```#Dashboard Service```
 
	```dashboard:```
 
  		```config:```
 
    			```spinnakerLink: <<SPINNAKER DECK URL Example spinaker.opsmx.net>>```

4. Authentication:

	* **Using LDAP**

		Make changes in the ldap section in values-APforOSS.yaml

		```#ldap configuration used in oes-gate, oes-platform and spinnaker gate for authentication and authorization```

		```#Change the below settings based on your LDAP server```

		```Ldap:```
 
  			```enabled: true```
 
  			```url: << LDAP URL: Example: ldaps://xxx.opsmx.com:636 >>```
 
  			```managerDn: cn=manager,dc=opsmx,dc=com```
 
  			```managerPassword: manager123```
 
  			```groupSearchBase: ou=groups,dc=opsmx,dc=com```
 
  			```groupSearchFilter: member={0}```
 
  			```groupRoleAttributes: cn```
 
  			```userDnPattern: uid={0},ou=users,dc=opsmx,dc=com```

		**Note**:

		**managerDn**: The Distinguished Name (DN) used to log into the Directory Service and to search for user accounts.
 
		**manager-password**: The password for the manager account specified in the managerDn property.
 
		**groupSearchBase**: The DN of the LDAP object where the search for the user account's groups begins.
 
		**groupSearchFilter**: The LDAP query string used to find the user account's group objects. The default is “(member={0})”. (In some LDAP implementations the name is memberof.). The {0} is a required value. It is a token that represents the user account that is being validated
 
		**groupRoleAttributes**: The field name to use as the Security role name for the group object DN

		**userDnPattern**: The field name to tell the authenticator how to find a user in LDAP


	* **Using SAML 2.0**

		Make changes in the saml section in values-APforOSS.yaml

		```saml:```
 
  			```enabled: true```

  			```userSource: gate # Groups will be obtained from SAML```
 
  			```keyStore: /opt/spinnaker/saml/oessaml.jks # The key in this secret must be oessamljks```
 
  			```keyStorePassword: changeit```
 
  			```keyStoreAliasName: saml```
 
  			```metadataUrl: /opt/spinnaker/saml/oesmetadata.xml # The key in this secret must be oesmetadataxml```
 
  			```redirectProtocol: https```
 
  			```redirectHostname: << AUTOPILOT GATE URL Example autopilot-gate.opsmx.net >>```
 
  			```redirectBasePath: /```
 
  			```issuerId: << Unique Issuer Id Example opsmx.test >>```
 
  			```jksSecretName: oessamljks```
 
  			```metadataSecretName: oesmetadataxml```


	* **Using OAuth 2.0**

		Autopilot supports OAuth 2.0 for authentication with GitHub organizations. Consult the GitHub OAuth 2.0 documentation and register a new OAuth 2.0 application to obtain a client ID and client secret. Make changes in the oauth2 section in values-APforOSS.yaml

		```oauth2:```
 
  			```enabled: true```
 
  			```client:```
 
    				```clientId: #CLIENT_ID```
 
    				```clientSecret: #CLIENT_SECRET_ID```
 
    				```accessTokenUri: https://github.com/login/oauth/access_token```
 
    				```userAuthorizationUri: https://github.com/login/oauth/authorize```
 
    				```scope: user-email```
 
  			```resource:```
 
    				```userInfoUri: https://api.github.com/user```
 
  			```userInfoMapping:```
 
		```email: email```
 
    				```firstName: firstname```
 
    				```lastName: name```
 
    				```username: login```
 
  			```provider: GITHUB```


5. Specify the user groups from the authentication system; these groups will have Super Administrator privileges in Autopilot. Specify userSource for the specific authorization type.

	```platform:```
 
  		```config:```
 
    			```#These groups will have superAdmin privileges in Autopilot adminGroups: admin```

    			```#Source of groups for Authorization```

    			```#Support sources: LDAP, FILE, GATE. In general, use "gate" for SAML```

    			```userSource: ldap```

6. Specify Spinnaker Gate URL. When the Authentication type is X509, set the corresponding flag to true.

	```sapor:```
 
  		```config:```
 
    			```spinnakerImages: OSS```
 
    			```spinnaker:```
 
    			```#true if authentication is enabled in Spinnaker authnEnabled: true```
 
    			```#encryption key is needed for sapor to startup```
 
    			```encrupt:```
 
      				```enabled:false```

7. Add opsmx helm chart using the command

	```$ helm repo add opsmx https://helmcharts.opsmx.com/```
 
	```$ helm repo update```

	Create a namespace for installing Autopilot
 
	```$ kubectl create namespace autopilot```

	Begin the installation using the following command
 
	```$ helm install myautopilot opsmx/oes -f values-APforOSS.yaml -n autopilot --timeout 60m```

8. Run the following command to get the URLs

	```$kubectl get ingress -n autopilot```

	IP addresses should be linked to URLs in the DNS server 


##**Integrate Autopilot with Open Source Spinnaker**##

After installing Autopilot, there are few changes in your Open Source Spinnaker configuration to integrate it with Autopilot. Following are the changes:

1. Before editing yaml files in Spinnaker, first ensure their persistence. If they are not persistent already, follow these steps

	*Example:* 

	```$ kubectl edit cm ossspin-spinnaker-halyard-init-script``` 

	Where ossspin is the name of the spinnaker instance. Change it as per your instance name. The following lines need to be commented out, followed by save.

 	```# rm -rf /tmp/spinnaker/.hal/default/service-settings```

 	```# cp /tmp/service-settings/*/tmp/spinnaker/.hal/default/service-settings```

 	```# rm -rf /tmp/spinnaker/.hal/default/profiles```

 	```# cp /tmp/additionalProfileConfigMaps/*/tmp/spinnaker/.hal/default/profiles/```

2. Enable echo events in OSS Spinnaker. Get a shell to the Spinnaker halyard pod. 

	*Example:*

	```kubectl exec -it oes-spinnaker-halyard-0 -n oss-spin -- /bin/bash```

	Go to location: ```~/.hal/default/profiles``` edit the file **echo-local.yml**;

	Create a new file if not available 

	**$ vi echo-local.yml**

	Update/Add the following lines, with the correct "Autopilot Gate" URL.

   	```rest:```
     		```enabled: true```
     		```endpoints:```
       			```-```
         		```wrap: false```
         		```url: << AUTOPILOT GATE URL``` 
      			```Example https://autopilot-gate.opsmx.net >>/auditservice/v1/echo/events/data``` 
       			```-```
         			```wrap: false```
         			```url: << AUTOPILOT GATE URL```
      			```Example https://autopilot-gate.opsmx.net >>/oes/echo```

3. Enable Custom Plugins in Spinnaker. Get a shell to the Spinnaker halyard pod.

	*Example:*

	```$kubectl exec -it spinnaker-halyard-0 -n oss-spin -- /bin/bash```

	Go to location: ~/.hal/default/profiles
 
	```$ cd ~/.hal/default/profiles```
 
	Edit the file orca-local.yml (create a new file if not available)

	```$ vi orca-local.yml``` 

	Update/Add the following lines

	```
  	spinnaker:

    	extensibility:

      	plugins:

        	Opsmx.VerificationGatePlugin:

          	enabled: true

          	version: 1.0.1

          	config:

        	Opsmx.VisibilityApprovalPlugin:

          	enabled: true

          	version: 1.0.1

          	config:

        	Opsmx.TestVerificationGatePlugin:

          	enabled: true

          	version: 1.0.1

          	config:

        	Opsmx.PolicyGatePlugin:

          	enabled: true

          	version: 1.0.1

          	config:

      	repositories:

        	opsmx-repo:

          	id: opsmx-repo

          	url:
      
 		https://raw.githubusercontent.com/OpsMx/spinnakerPluginRepository/v3.10.0/plugins.json
	```


	Edit the file gate-local.yml (create a new file if not available)

	$ vi gate-local.yml

	Update/add the following lines

	```
  	spinnaker:

   	  extensibility:

      	     plugins:

      		deck-proxy:

        	  enabled: true

        	  plugins:

          		Opsmx.VerificationGatePlugin:

            		  enabled: true

            		  version: 1.0.1

          		Opsmx.TestVerificationGatePlugin:

            		  enabled: true

            		  version: 1.0.1

          		Opsmx.PolicyGatePlugin:

            		  enabled: true

            		  version: 1.0.1

          		Opsmx.VisibilityApprovalPlugin:

            		  enabled: true

            	 	  version: 1.0.1

      	     plugins-root-path: /opt/gate/plugins

      	     repositories:

               opsmx-repo:

          	  url:
 
	https://raw.githubusercontent.com/OpsMx/spinnakerPluginRepository/v3.10.0/plugins.json
	```

	Edit the file front50-local.yml (create a new file if not available) 

	$ vi front50-local.yml

	Update/add the following lines

	```
	  spinnaker:
    	extensibility:
      	plugins:
        	Opsmx.StaticPolicyPlugin:
          	enabled: true
          	version: "1.0.1"
          	config: null
      	repositories:
        	opsmx-repo:
          	id: "opsmx-repo"
          	url: "https://raw.githubusercontent.com/OpsMx/spinnakerPluginRepository/v3.10.0.staticpolicy/plugins.json" 
  	policy: 
    	opa: 
      	enabled: true 
      	url: << AUTOPILOT GATE URL Example https://autopilot-gate.opsmx.net >>
	```

4. Run Hal deploy apply command to reflect the changes 

	```$ hal deploy apply```

5. Configure Spinnaker in Autopilot UI Once Spinnaker and OES are installed successfully, we need to configure Spinnaker in Autopilot UI. 

	Navigate to Setup → Spinnaker → Add Spinnaker. 

	The following fields need to be provided: 

	* Spinnaker Name: User defined name of Spinnaker Instance
 
	* Spinnaker Gate URL: The Gate URL of the Spinnaker instance
 
	* Authentication Type: Mechanism with which Autopilot communicates to Spinnaker.

	* Mention, LDAP, for Basic Authentication (username with password) or X509, for 2 Factor Authentication systems.

	**Using the LDAP Mechanism**

	Provide the username and password of the user who has the access to all the pipelines in the encrypted base64 format using this command, 
	
	```echo -ne “username:password” | base64 -w0```


**Using the x509 Mechanism**

* To Enable x509 method of Authentication for Spinnaker, please refer to the steps in
 
	[https://spinnaker.io/setup/security/authentication/x509/](https://spinnaker.io/setup/security/authentication/x509/)

* For Autopilot to communicate with Spinnaker using x509, we need a client p12 key.
 
* Using the client.p12 key and password, we can configure Autopilot to communicate with Spinnaker on the x509 port.
 
* Assuming that we have client tls certificate and key, we can generate a p12 using the following command,
 
	```$ openssl pkcs12 -export -clcerts -in x509lb.crt -inkey x509lb.key -out x509lb.p12 -name gate509lb -password pass:changeit```

