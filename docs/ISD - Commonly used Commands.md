#**ISD - Commonly used Commands**#

##**Basic commands for OpsMx ISD**##

**This document contains a list of basic commands that can be useful when working with OpsMx ISD.**

1. **Working with pods**

	* ```kubectl get pods -n <namespace>``` # to list down pods

	* ```kubectl delete po <pod-id> -n <namespace>``` #for deleting the pods

	* ```kubectl exec -it <pod id> -n <namespace>``` -- bash # for getting into the pod

	* ```kubectl scale deploy <deployment-name> -n <name-space> --replicas=0``` #for scaling down the pods

	* ```kubectl scale deploy -n --replicas=1``` #for scaling up the pods 

	* ```kubectl describe pod -n``` # to describe a pod
 
	* ```kubectl logs -f -n``` #to get the running status of a pod
 
	* ```kubectl logs -f -n >``` #to get the running status of a pod into a file
 
	* ```kubectl apply -f <filename.yaml> -n``` #to create a service/secret/job


2. **Working with Secrets**

	* ```kubectl get secrets -n``` #to get all the secrets
 
	* ```kubectl get secret -n -o yaml``` #to view a particular secret
 
	* ```kubectl get secret -n -o yaml``` #to edit the secret
 
	* ```echo -n <encode/decode-content> | base64 -d/-e -w0``` #to encode/decode a secret

	* Creating platform secrets

		* ```kubectl -n get secrets oes-platform-config -o jsonpath=‘{.data.platform-local.yml}’ | base64 -d > platform-local.yml``` 

		If needed, edit the platform-local.yml file

		* ```kubectl -n delete secret oes-platform-config```
 
		* ```kubectl -n create secret generic oes-platform-config --from-file platform-local.yml```

	* Creating gate secrets 

		* ```kubectl -n get secrets oes-gate-config -o jsonpath=‘{.data.gate.yml}’ | base64 -d > gate.yml``` 

		If needed, edit the gate.yml file 

		* ```kubectl -n delete secret oes-gate-config```
 
		* ```kubectl -n create secret generic oes-gate-config --from-file gate.yml```

	* Creating sapor secrets

		* ```kubectl -n get secrets oes-sapor-config -o jsonpath='{.data.application.yml}' | base64 -d > application.yml```

		If needed, edit the application.yml file

		* ```kubectl -n delete secret oes-sapor-config``` 

		* ```kubectl -n create secret generic oes-sapor-config --from-file application.yml```

3. **Working with deployments**

	* ```kubectl get deploy  -n <namespace>``` # to get list of all the deployments

	* ```kubectl get deploy  <deployment-name> -n <namespace> -o yaml``` #to view a particular deployment

	* ```kubectl edit deploy <deployment-name> -n <namespace>``` #to edit a deployment

	* ```kubectl scale deploy <deployment-name> --replicas=0  -n <namespace>``` #to scale-down a deployment

	* ```kubectl scale deploy <deployment-name> --replicas=1  -n <namespace>``` #to scale-up a deployment

	* ```echo -n <encode/decode-content> | base64 -d/-e -w0``` #to encode/decode a deployment

4. **Working with Configmaps**

	* ```kubectl get cm -n``` # to get the list of all configmaps
 
	* ```kubectl get cm -n -o yaml``` # to get the details of the configmap
 
	* ```kubectl edit cm -n``` # to edit the configmap

5. **Working with services**

	* ```kubectl get svc -n``` # to get the list of all services 

	* ```kubectl get svc -n -o yaml``` # to view a service
 
	* ```kubectl edit svc -n``` # to edit a service

6. **Working with the jobs**

	* ```kubectl get jobs -n``` # to get the list of all jobs
 
	* ```kubectl get job -n -o yaml``` # to view a job 

	* ```kubectl describe job -n -o yaml``` # to describe a job
 
	* ```kubectl edit job -n``` # to edit a job
 
	* ```kubectl delete job -n``` # to delete a job

7. **To check the ingress**

	* ```kubectl get ing -n``` # to get the ingress
