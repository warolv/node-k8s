## Create Delivery Pipeline with Jenkins on GKE for node app with 3 replicas and LB.

### Create cluster with GKE

### Helm must be installed

wget https://storage.googleapis.com/kubernetes-helm/helm-v2.14.1-linux-amd64.tar.gz
tar zxfv helm-v2.14.1-linux-amd64.tar.gz 
cp linux-amd64/helm .


### To give yourself cluster administrator permission in the cluster’s RBAC:

kubectl create clusterrolebinding cluster-admin-role --clusterrole=cluster-admin --user=$(gcloud config get-value account)

### To give Tiller cluster-admin role:

kubectl create serviceaccount tiller-server --namespace kube-system
kubectl create clusterrolebinding tiller-admin-role --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-server

### Install jenkins, using load balancer service to be publically accessible:

helm install jenkins-tool stable/jenkins --set master.serviceType=LoadBalancer

### Configure the Jenkins service account to be able to deploy to the cluster:

kubectl create clusterrolebinding jenkins-deploy --clusterrole=cluster-admin --serviceaccount=default:jenkins-tool

### In Jenkins / global credentials, add:

  Kubernetes Service Account
  Google Service Account from metadata

### Get your 'admin' user password for Jenkins by running:

printf $(kubectl get secret --namespace default jenkins-tool -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo

### Need to enable services on GKE cluster:

https://console.cloud.google.com/flows/enableapi?apiid=compute_component,container,cloudbuild.googleapis.com

### Good reference: 

https://cloud.google.com/solutions/continuous-delivery-jenkins-kubernetes-engine
https://github.com/GoogleCloudPlatform/continuous-deployment-on-kubernetes