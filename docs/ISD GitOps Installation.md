#**ISD GitOps Installation**#

The document is primarily intended to be used for standard ISD GitOps installation.
 
##**Infrastructure and Laptop requirements**##

Before you start, it might be helpful to go through these documents: 

* The infrastructure required for a non-prod installation can be found [here](https://docs.opsmx.com/release-history/previous-releases/isd-4.0/operator-manual/installation-and-configuration/isd-on-prem-pov-infrastructure-requirements)

* The infrastructure required for a Production Setup can be found [here](https://docs.opsmx.com/release-history/previous-releases/isd-4.0/operator-manual/installation-and-configuration/isd-on-prem-production-infrastructure-requirements)

* Basic requirements of a laptop and Kubernetes cluster can be found [here](https://docs.opsmx.com/release-history/previous-releases/isd-4.0/operator-manual/installation-and-configuration/environment-setup-for-opsmx-isd).

If you need a different infrastructure, please [contact](https://www.opsmx.com/contact/) OpsMx.


##**Create your git-repo**##

ISD stores all the configuration in a repo, typically a '**git repo**', though bitbucket, S3, and others are supported.

1. Create an empty-repo (called the "gitops-repo" in the document), "main" branch should be the default and clone it locally.

2. Clone [https://github.com/OpsMx/standard-isd-gitops](https://github.com/OpsMx/standard-isd-gitops), selecting the appropriate branch:

	```git clone https://github.com/OpsMx/standard-isd-gitops -b 4.0```

3. Copy contents of the standard-isd-repo to the gitops-repo created above using:

	```cp -r standard-isd-gitops/* gitops-repo``` # Replace "gitops-repo" with your repo-name and cd to the gitops-repo e.g. ```cd gitops-repo```.


	##**Specify inputs**##

	Specify the inputs based on your environment and git-repo. The installation process requires inputs such as the application version, git-repo details, etc.

4. In the gitops-repo cloned to disk and edit install/inputcm.yaml. This should be updated, at a minimum, with gitrepo and username.

5. **Update Values.yaml as required**, specifically: At **minimum** the ISD URL and gitops-repo details in the spinnaker.gitopsHalyard section must be updated. Full values.yaml is available at the following link : 

	[https://github.com/OpsMx/enterprise-spinnaker/tree/v3.12/charts/oes](https://github.com/OpsMx/enterprise-spinnaker/tree/v3.12/charts/oes)

	!!! Note
   		We recommend that we start with the defaults, updating just the URL and gitopsHalyard details and gradually adding SSO, external DBs, etc. while updating the installed instance.

6. Push all changes in the gitops-repo to git (E.g ```git add -A; git commit -m"my changes";git push```).

7. Create namespace, a configmap for inputs and a service account as follows:

	```
	kubectl create ns opsmx-isd
	kubectl -n opsmx-isd apply -f install/inputcm.yaml
	kubectl -n opsmx-isd apply -f install/serviceaccount.yaml # Edit namespace in the yaml
	if changed from the default and update the kubectl command.
	```

	##**Create secrets**##

	ISD supports multiple secret managers for storing secrets such as DB passwords, SSO authentication details, and so on. Using Kubernetes secrets is the default.

8. Create the following secrets. The default values are handled by the installer, except for gittoken. If you are using External SSO, DBs, etc. you might want to change them. Else, best to leave them at the defaults:

	```
	kubectl -n opsmx-isd create secret generic gittoken --from-literal=gittoken=PUT_YOUR_GITTOKEN_HERE
	```


	**Optional**

	In case we want to change these, please enter the correct values and create the secrets

	```
	kubectl -n opsmx-isd create secret generic ldapconfigpassword --from-literal ldapconfigpassword=PUT_YOUR_SECRET_HERE
	kubectl -n opsmx-isd create secret generic ldappassword --from-literal ldappassword=PUT_YOUR_SECRET_HERE
	kubectl -n opsmx-isd create secret generic miniopassword --from-literal miniopassword=PUT_YOUR_SECRET_HERE
	kubectl -n opsmx-isd create secret generic redispassword --from-literal redispassword=PUT_YOUR_SECRET_HERE
	kubectl -n opsmx-isd create secret generic saporpassword --from-literal saporpassword=PUT_YOUR_SECRET_HERE
	kubectl -n opsmx-isd create secret generic rabbitmqpassword --from-literal rabbitmqpassword=PUT_YOUR_SECRET_HERE
	kubectl -n opsmx-isd create secret generic keystorepassword --from-literal keystorepassword=PUT_YOUR_SECRET_HERE
	```

	##**Start the installation**##

	The installation is done by a Kubernetes job that processes the secrets, generates YAMLs, stores them into the git-repo and creates the objects in Kubernetes.

9. Installation of ISD by executing this command:

	```kubectl -n opsmx-isd apply -f install/ISD-Install-Job.yaml```


	##**Monitor the installation process**##

10. Wait for all pods to stabilize (about 10-20 min, depending on your cluster load). The "oes-config" in Completed status indicates completion of the installation process. Check the status using the following comment: ```kubectl -n opsmx-isd get po -w```

	**Note:** If the pod starting with isd-install-* errors out, please check the logs as follows, replacing the pod-name correctly:

	```
	kubectl -n opsmx-isd logs isd-install-tjzlx -c get-secrets
	kubectl -n opsmx-isd logs isd-install-tjzlx -c git-clone
	kubectl -n opsmx-isd logs isd-install-tjzlx -c apply-yamls
	```

	**Note:** It is normal for some pods, specifically the oes-ui pod to crash a few times before running. However, if the isd-spinnaker-halyard-0 pod crashes or errors out, please check the logs of the "create-halyard-local" init container using this command:

	```kubectl -n opsmx-isd logs isd-spinnaker-halyard-0 -c create-halyard-local```


	##**Check the installation**##

11. Access ISD using the URL specified in the values.yaml in step 5 in a browser such as Chrome.

12. Login to the ISD instance with user/password as admin and opsmxadmin123, if using the defaults for build-in LDAP.

!!! Note
   	If you are facing any Issues during installation, refer to the [Troubleshooting](https://docs.opsmx.com/release-history/previous-releases/isd-4.0/additional-resources/troubleshooting#isd-gitops-installation-issues) page.






 
 









 

