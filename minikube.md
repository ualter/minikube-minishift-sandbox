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
## Login
```bash
$ docker login
```

#### Direct Command specifyng the SSL Certificate, needed to work with Kubernetes. Without the SSL https, the Kubernetes will not access the Registry
```bash
 $ sudo docker run -d -p 5000:5000 --restart=always --name my-registry \
    -v /Users/ualter/Developer/minikube-minishift/my-registry/certs:/certs \
    -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
    -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
    registry:2
```

#### (At /etc/hosts the register [Your Local IP]  my-registry)
#### The command must already been working
```bash
 $ curl -s http://my-registry:5000/v2/_catalog | jq .
```

#### Registering an Image to your local registry
```bash
 $ docker tag envoy-service1 my-registry:5000/envoy-service1
 ```

#### Push to the local registry running localhost:5000
```bash
 $ docker push my-registry:5000/envoy-service1
 ```

#### Remove the locally-cached and from localhost, so we can try pulling the image from the registry
```bash
 $ docker rmi front-proxy_service1
 $ docker rmi localhost:5000/my-service1
 ```

#### Pull from our Registry
```bash
 $ docker pull localhost:5000/my-service1
#### Check
$ curl https://my-registry:5000/v2/_catalog
$ curl https://my-registry:5000/v2/envoy-service1/tags/list
## More: https://docs.docker.com/registry/deploying/
```

## On the Kubernetes
```bash
 $ minikube ssh
 ```
#### Create a ca.crt file for your Self-Signed Certificate at the Kubernetes Linux
```bash
 $ vi sudo /etc/docker/certs.d/my-registry:5000/ca.crt ## and put public Certificate here
 ```
#### If still does not work, put the same public Certificate (your self signed) at this file also:
 $ sudo vi /etc/ssl/certs/ca-certificates.crt
#### Inside the Kubernetes environment should work this command
```bash
 $ docker pull my-registry:5000/envoy-service1
 ```

#### Copy Files to Minikube
```bash
sudo scp -i $(minikube ssh-key) service-envoy.yaml docker@$(minikube ip):/Users/ualter
```

#### Deploy to Kubernetes
```bash
$ kubectl create deployment envoy-service1 --image=my-registry:5000/envoy-service1
## or
$ kubectl apply -f deployment-service1.yaml
```

#### Create a Service (LoadBalancer type) from the Deployment (Do not work on Minikube)
```bash
$ kubectl expose deployment/service1-deployment --type=LoadBalancer --name=service1
```

#### Create a Service (NodePort type) from the Deployment
```bash
$ kubectl expose deployment/service1-deployment --type=NodePort --name=service1
## To acces it:
## 1. Get the NodeIp:
$ kubectl cluster-info
kubernetes master is running at https://192.168.99.100:8443 #grab the IP, 192.168.99.100
## 2. Get the Service NodePort
$ kubectl get svc -o wide #grab the NodePort
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE       SELECTOR
service1     NodePort    10.97.139.199   <none>        80:30532/TCP   7m        app=service1 #here it is the 30352
## 3. So...
$ curl -v http://192.168.99.100:30352/service/1
```

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

### Extra
#### Create Self Signed Certificate
```bash
- openssl req -newkey rsa:4096 -nodes -sha256 -keyout certs/domain.key -x509 -days 365 -out certs/domain.crt
```

#### Copy file to Minikube
```bash
$ sudo scp -i $(minikube ssh-key) service-envoy.yaml docker@$(minikube ip):/Users/ualter
```
##### In case Error:
```bash
$ ECDSA host key for 192.168.99.100 has changed and you have requested strict checking.
Host key verification failed.
lost connection
```
##### Run to fix it:
```bash
sudo ssh-keygen -R 192.168.99.100
```