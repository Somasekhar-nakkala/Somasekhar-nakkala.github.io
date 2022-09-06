#**Installing Autopilot**#
If you are already using Open Source Spinnaker, then Autopilot can get seamlessly integrated with your Open Source Spinnaker and you get the all the benefits of Autopilot product. The following section defines the procedure for installing Autopilot as a standalone module to work with Open Source Spinnaker.

##**Prerequisites**##
1. Have access to public repositories in docker.io and quay.io 
2. Have the following tools installed - wget, kubectl, helm 
3. Have access to a Kubernetes cluster with at least 2 nodes, each node having 8 CPU and 32 GB RAM 
4. Have the NGINX Ingress Controller installed in the cluster. If it is not already installed, then you can install the same using the following instructions  
	4.1. kubectl create ns ingress-nginx

	4.2. helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
 
	4.3. helm repo update

	4.4. helm install ingress-nginx ingress-nginx/ingress-nginx -n ingress-nginx
 
	4.5. kubectl get svc -n ingress-nginx

5. Have “cert-manager” already available in the cluster. If it is not already available, then you can install it using the following instructions

	5.1. kubectl create namespace cert-manager

	5.2. helm repo add jetstack https://charts.jetstack.io
 
	5.3. helm repo update
 
	5.4. helm install cert-manager jetstack/cert-manager --set installCRDs=true -n cert-manager

6. In the Kubernetes cluster, have 3 Persistent Volumes of size 10GB each
7. Two DNS records pointing to the IP of the Ingress Controller for the following: 
    * “Autopilot UI”, Eg. autopilot.opsmx.net 
    * “Autopilot Gate”, Eg. autopilot-gate.opsmx.net