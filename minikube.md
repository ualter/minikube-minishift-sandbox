## Minikube

## Start and Config
# 
minikube start 

# config (just first time)
kubectl config use-context minikube

#
kubectl cluster-info

#
minikube dashboard

# Because this tutorial uses Minikube, instead of pushing your Docker image to a registry, 
# you can simply build the image using the same Docker host as the Minikube VM, 
# so that the images are automatically present. 
# To do so, make sure you are using the Minikube Docker daemon
eval $(minikube docker-env)

# Note: Later, when you no longer wish to use the Minikube host, 
# you can undo this change by running:
eval $(minikube docker-env -u)


## Build a Docker Image

# Build any Docker Image to deploy its Container(s) in Minikube Pods
docker build -t hello-node:v1 .

## Deploy a Docker Image 
# Deploy the NodeJs App
# Create a Deployment that manages a Pod. The Pod runs a Container based on the image
kubectl run hello-node --image=hello-node:v1 --port=8080

## Other Commands to view Status (Dashboard)
# 
kubectl get deployments
#
kubectl get pods
#
kubectl get eventskubectl get events
#
kubectl config view

## Create Service

# Expose the Pod the the Public Internet
kubectl expose deployment hello-node --type=LoadBalancer

# 
kubectl get services

# The --type=LoadBalancer flag indicates that you want to expose your Service outside of the cluster.
# On cloud providers that support load balancers, an external IP address would be provisioned to access the Service. 
# On Minikube, the LoadBalancer type makes the Service accessible through the minikube service command.
#
minikube service hello-node (open browser)
minikube service hello-node --url (the url)


#
kubectl logs <POD-NAME>


# Update image of a Deployment (new Image version)
kubectl set image deployment/hello-node hello-node=hello-node:v2


#
minikube addons list

# Enable addon
minikube addons enable heapster

# Open Heapster
minikube addons open heapster

# Show Pods and Services (Info)
kubectl get po,svc -n kube-system


## Clean Up Things

#
kubectl delete service hello-node
#
kubectl delete deployment hello-node

## Stop Everything!
#
minikube stop
#
eval $(minikube docker-env -u)


## More...

#
kubectl exec -ti [pod-name]  bash
kubectl exec -ti hello-node-6ff6665d75-gmmm2  bash


#
kubectl scale deployment hello-node --replicas=4