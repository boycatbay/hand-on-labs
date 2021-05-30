# Hand-On Lab

This lab is running on Kubernetes in Google Cloud Platform.
I have used Cloudbuild as CI/CD and Gitlab as SVC.
The cluster consists of 4 nodes in 2 AZs in the region Singapore. 


## Setup Nginx Ingress & Redis

For setting up Cluster, we will need to deploy Nginx Ingress Controller and Redis before proceeding with Application deployment

For Nginx Ingress Controller
First, you need to create the RBAC to deploy it

```bash
kubectl create clusterrolebinding cluster-admin-binding \
  --clusterrole cluster-admin \
  --user $(gcloud config get-value account)
```
Then, apply the deployment file from the folder Nginx Configuration.

```bash
kubectl apply -f ./nginx-configuration/nginx-ingress-beta.yaml #for beta env ingress
kubectl apply -f ./nginx-configuration/nginx-ingress-prod.yaml #for prod env ingress
```

Next, we will need to deploy Redis that the application 'hello1' need to use for record request and timestamp

```bash
kubectl apply -f ./kubernetes/redis-beta.yaml #for beta env redis
kubectl apply -f ./kubernetes/redis-prod.yaml #for prod env redis
```

## Build & Deploy & Run application

The next step is building the application, we use Cloudbuild which is the CI/CD service in GCP. The code for the CI/CD  stores at the location.
```bash
./hand-on-labs/cloudbuild/<app name>/cloudbuild.yaml
```

When the user commits the code to Git, Cloudbuild will read this configuration and execute the docker build process and deploy it to Container Registry

Then, it will automatically deploy the update to Kubernetes using the deployment template stores at the location.
```bash
./hand-on-labs/kubernetes/<app name>-deploy/deployment.yaml
```

Note: For changing the Redis Port and Hostname, you can edit the value in Cloudbuild template at Substitution Part in value REDIS_PORT and REDIS_HOST

After the pipeline is finished, you need to apply the ingress config for each app manually using the template in the folder Nginx Configuration.
```bash
kubectl apply -f ./nginx-configuration/<app name>.yaml 
```

## Testing application

After finished all step, you can test the access by using a web browser to access the URL which in the format
```bash
http://<EXTERNAL-IP of the load balancer of each env>/hello1 # for App Hello1
http://<EXTERNAL-IP  of the load balancer of each env>/hello2 # for App Hello2
```
To get the IP of the load balancer, you can use the command below.
```bash
kubectl get services -A | grep LoadBalancer
```
Which will show a result like this one.
```bash
NAMESPACE            NAME                                 TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)                      AGE              
ingress-nginx-prod   ingress-nginx-controller             LoadBalancer   10.220.9.22     35.198.228.111   80:31722/TCP,443:31119/TCP   82m
ingress-nginx        ingress-nginx-controller             LoadBalancer   10.220.4.57     34.126.175.36    80:31490/TCP,443:31321/TCP   12h
```