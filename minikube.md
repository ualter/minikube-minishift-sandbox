# Minikube

##### Start and Config
```bash
minikube start 
```

##### Config (just first time)
```bash
kubectl config use-context minikube
```
##### Check...
```bash
kubectl cluster-info
```
##### Dashboard
```bash
minikube dashboard
```

### Docker
##### Config Docker to use the Docker Daemon of Minikube Installation
```bash
eval $(minikube docker-env)
```
##### Get Back the Docker to normal...
```bash
eval $(minikube docker-env -u)
```
##### Local Docker Register for Kubernetes 
```bash
// Login
 $ docker login
// Create a local registry, an Instance from Image Docker
 $ sudo docker run -d -p 5000:5000 --restart=always --name registry registry:2 
// registering an Image to your local registry
 $ docker tag front-proxy_service1 localhost:5000/my-service1 
// Push to the local registry running localhost:5000
 $ docker push localhost:5000/my-service1
// Remove the locally-cached and from localhost, so we can try pulling the image from the registry
 $ docker rmi front-proxy_service1
 $ docker rmi localhost:5000/my-service1
// Pull from our Registry
 $ docker pull localhost:5000/my-service1
//List Repositories
 $ curl -s -X GET http://localhost:5000/v2/_catalog | jq .
// More: https://docs.docker.com/registry/deploying/
```

##### Config Minikube with the Docker Registry Local
- kubectl create -f kube-registry.yaml
- kubectl port-forward --namespace kube-system
$(kubectl get po -n kube-system | grep kube-registry-v0 | \awk '{print $1;}') 5000:5000


##### Build any Docker Image to deploy its Container(s) in Minikube Pods
```bash
docker build -t hello-node:v1 .
```

##### Deploy a Docker Image 
```bash
kubectl run hello-node --image=hello-node:v1 --port=8080
```
##### More...
```bash
kubectl get deployments
kubectl get pods
kubectl get eventskubectl get events
kubectl config view
```
##### Create Service
```bash
kubectl expose deployment hello-node --type=LoadBalancer
```
##### Check it
```bash
kubectl get services
```
```bash
minikube service hello-node (open browser)
minikube service hello-node --url (the url)
```
##### Logs
```bash
kubectl logs <POD-NAME>
```
##### Update image of a Deployment (new Image version)
```bash
kubectl set image deployment/hello-node hello-node=hello-node:v2
```
##### Addons
```bash
minikube addons list
```
##### Enable Addon
```bash
minikube addons enable heapster
```
##### Open Heapster
```bash
minikube addons open heapster
```
##### Show Pods and Services (Info)
```bash
kubectl get po,svc -n kube-system
```
##### Clean Up Things
```bash
kubectl delete service hello-node
kubectl delete deployment hello-node
```
##### Stop Everything!
```bash
minikube stop
eval $(minikube docker-env -u)
```
##### Bash
```bash
kubectl exec -ti [pod-name]  bash
kubectl exec -ti hello-node-6ff6665d75-gmmm2  bash
```
##### Scale
```bash
kubectl scale deployment hello-node --replicas=4
```
