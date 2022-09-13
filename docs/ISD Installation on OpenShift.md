#**ISD Installation on OpenShift**#

##**Installing ISD on OpenShift platform**##

Refer this detailed instructions for installing ISD on OpenShift platform.

###**Begin your installation:**###

Click Download OES-3.9.1 and README.md. The YAML file for the OES installation and its README will download to your computer.


###**Pre-requisites**###

1. Download and extract the OES Package “tar -xvf OES-.tar.gz”.
 
2. Provide the user's quay credentials to get access to the images.
 
3. Ensure to have a service account that allows the OES Installation process.


###**Installation Steps**###

1. Navigate to the Directory. 

	```cd OES-/charts/oes```

2. Edit the values.yaml and update the "imageCredentials" as below. 

	```registry: https://quay.io/``` 

	```username: # Quay username```

	```password: # Quay password``` 

	```email: emailaddress@domain.com # email corresponding to quay``` 

	```registry ID```

3. Enable True for the below if needed.

	* CertManager
 
	* Ingress

	* OpenLDAP

4. If you are using an existing extern Openshift Route/Ingress, ensure to update the URLs in the below fields. So that all the spinnaker and OES configurations will be done during the installation.

	* Update the GitOps Halyard Section with BitBucket/S3 where ever Halyard is placed.

	* Update the Pipeline Promotion with BitBucket/S3 credentials according to your requirement.

		```Spinnaker Deck URL configuration; url overwhich spinnaker deck will be accessed```
 
		```Spinnaker Gate URL configuration; url overwhich spinnaker gate will be accessed```
 
		```OES-UI url configuration```
 
		```OES-Gate url configuration```

5. Update the LDAP details, if you are using a different LDAP.

6. Execute a helm install command to start the installation. 

	```cd OES-/charts/oes/```
 
	```helm install oes . --namespace --timeout 20m```

	```E.g. helm install oes . --namespace oes --timeout 20m```


7. After completion of installation, below containers will be up and running.

  	<head>
    	<title>Title of the document</title>
    	<style>
      	table,
      	th,
      	td {
        	padding: 2px;
        	border: 1px solid black;
        	border-collapse: collapse;
      	}
    	</style>
  	</head>

	| NAME         | READY        | STATUS         | RESTARTS      | AGE          |
 	|:------------ |:------------:|:--------------:|:-------------:|:------------:|
 	| oes-autopilot-787685b89f-tkkjg | 1/1 | Running | 3 | 6h1m |
 	| oes-dashboard-5d4bbb6fcf-x8f7q | 1/1 | Running | 0 | 4h13m |
 	| oes-db-0 | 1/1 | Running | 0 | 5h46m |
 	| oes-gate-7b54554c5d-g7qs2 | 1/1 | Running | 0 | 5h27m |
 	| oes-install-using-hal-nkzkj | 0/1 | Completed | 0| 5h46m |  
 	| oes-minio-b56cd74df-srrhd | 1/1 | Running | 0 | 10h |
 	| oes-platform-6f55f98c96-72dxd | 1/1 | Running | 0 | 5h27m |
 	| oes-redis-master-0 | 1/1 | Running | 0 | 10h |
 	| oes-sapor-8cf5c9556-d9mpk | 1/1 | Running | 0 | 4h15m | 
 	| oes-spinnaker-halyard-0 | 1/1 | Running | 0 | 10h |
 	| oes-ui-6fc95cdf94-mj8fc | 1/1 | Running | 0 | 114m | 
 	| oes-visibility-889f6fcdb-vcwx7 | 1/1 | Running | 4 | 6h1m |
 	| opa-67c945d7b7-p9mjw | 1/1 | Running | 0 | 7h5m | 
 	| spin-clouddriver-caching-6bf67f45b8-wwtcj | 1/1 | Running | 0 | 5h37m |
 	| spin-clouddriver-ro-75f8d6bbbd-58fnn | 1/1 | Running | 0 | 5h37m | 
 	| spin-clouddriver-ro-deck-6b499cd544-69nbt | 1/1 | Running| 0| 5h37m|
 	| spin-clouddriver-rw-6f8ff48976-8c27m| 1/1 | Running | 0 | 5h37m | 
 	| spin-deck-7f74cd96d5-wcc5n | 1/1 | Running | 0 | 5h37m |
 	| spin-echo-scheduler-5fdd9fb7b-m7ztn | 1/1 | Running | 0 | 5h37m | 
 	| spin-echo-worker-5bf4b855fd-qzpp8 | 1/1 | Running | 0 | 5h37m |
 	| spin-fiat-59578d97f9-8mhvg | 1/1 | Running | 0 | 5h37m | 
 	| spin-front50-58c69476b8-pp9g4 | 1/1 | Running | 0 | 5h37m |
 	| spin-gate-6c6cdfc649-7lgvw | 1/1 | Running | 0 | 5h37m |
 	| spin-igor-85d986c45d-dphn5 | 1/1 | Running | 0 | 5h37m |
 	| spin-orca-7f666f4676-sgbzf | 1/1 | Running | 0 | 5h37m |
 	| spin-rosco-69fcc9cd7c-ppjnx | 1/1 | Running | 0 | 5h37m |


8. If you are not using any Ingress, create routes for the below services.

	```Oes-ui```
 
	```Oes-gate```
 
	```Spin-deck-lb```
 
	```Spin-gate-lb```

9. After the routes are created, update the URLs in the values.yaml in the ingress section and do a   “helm upgrade”. This will update all the override URLs in the spinnaker and other OES configurations.

	```Spinnaker Deck URL configuration; url overwhich spinnaker deck will be accessed```
 
	```Spinnaker Gate URL configuration; url overwhich spinnaker gate will be accessed```
 
	```OES-UI url configuration```
 
	```OES-Gate url configuration```

10. Enter the below command to execute a "helm upgrade" 

	```helm upgrade oes . --namespace oes --timeout 20m```













 
 




