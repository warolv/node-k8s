## Create Delivery Pipeline with Jenkins on GKE for node app with 3 replicas and LB.

1. Create cluster with GKE

2. Helm must be installed

3. To give yourself cluster administrator permission in the cluster’s RBAC:

kubectl create clusterrolebinding cluster-admin-role --clusterrole=cluster-admin --user=$(gcloud config get-value account)

4. To give Tiller cluster-admin role:

kubectl create serviceaccount tiller-server --namespace kube-system
kubectl create clusterrolebinding tiller-admin-role --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-server

5. Install jenkins, using load balancer service to be publically accessible:

helm install jenkins-tool stable/jenkins --set master.serviceType=LoadBalancer

6. Configure the Jenkins service account to be able to deploy to the cluster:

kubectl create clusterrolebinding jenkins-deploy --clusterrole=cluster-admin --serviceaccount=default:jenkins-tool

7. In Jenkins / global credentials, add:
  Kubernetes Service Account
  Google Service Account from metadata

8. Get your 'admin' user password for Jenkins by running:

printf $(kubectl get secret --namespace default jenkins-tool -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo

9. Need to enable services on GKE cluster:

https://console.cloud.google.com/flows/enableapi?apiid=compute_component,container,cloudbuild.googleapis.com

Good reference: 

https://cloud.google.com/solutions/continuous-delivery-jenkins-kubernetes-engine
https://github.com/GoogleCloudPlatform/continuous-deployment-on-kubernetes