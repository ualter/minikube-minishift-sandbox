# Minishift

## Install

#### Hypervisor
```bash
brew install docker-machine-driver-xhyve
sudo chown root:wheel $(brew --prefix)/opt/docker-machine-driver-xhyve/bin/docker-machine-driver-xhyve
sudo chmod u+s $(brew --prefix)/opt/docker-machine-driver-xhyve/bin/docker-machine-driver-xhyve
```
#### Check installation
```bash
brew info --installed docker-machine-driver-xhyve
```
## Start / Config
minishift start
####### One time, configure openshift client
```bash
eval $(minishift oc-env)
```
#### Configure Docker Console with Docker Daemon Minishift
```bash
eval $(minishift docker-env)
oc login -u system:admin
```
##### Log as Admin
```bash
oc login -u system:admin
```
##### IP
```bash
minishift ip
```
## Stop
```bash
eval $(minishift docker-env -u)
minishift stop
```
## Deploy the OpenShift NodeJs App Sample
```bash
oc new-app https://github.com/openshift/nodejs-ex -l name=myapp
oc logs -f bc/nodejs-ex
oc expose svc/nodejs-ex
minishift openshift service nodejs-ex --in-browser
```
##### Delete all objects of this deployment
```bash
oc delete all -l app=nodejs-ex
```

## Create Applications
###### (https://docs.openshift.com/enterprise/3.0/dev_guide/new_app.html)
## Using Docker Build Strategy
###### (https://docs.openshift.org/latest/architecture/core_concepts/builds_and_image_streams.html#docker-build)
```bash
 1. oc new-app /Users/ualter/Developer/minikube-minishift/nodeApp/ --name=nodeapp --strategy=docker --template=nodejs
 2. oc start-build nodeapp --from-dir=/Users/ualter/Developer/minikube-minishift/nodeApp/ --follow
 3. oc expose svc/nodeapp #expose to a route
 4. minishift openshift service nodeapp --in-browser #open it
 ```
#### Update the App (creating a new Image)
```bash
oc start-build nodeapp --from-dir=/Users/ualter/Developer/minikube-minishift/nodeApp/ --follow
```
#### Tag the new image
```bash
oc tag nodeapp:latest nodeapp:v2
```
#### Check
```bash
oc logs -f dc/nodeapp 
oc rollout history dc/nodeapp 
```
#### GIT specyfing branch developer
```bash
oc new-app https://github.com/openshift/ruby-hello-world.git#developer
```

## More...

#### List Projects
```bash
oc get projects
```
#### List Image Stream
```bash
oc describe is
```
#### List all local templates
```bash
oc new-app --list
```

#### Delete an App (all its objects)
```bash
oc delete all -l app=nodeapp
```

#### Configure Docker Console with Docker Daemon Minishift
```bash
eval $(minishift docker-env)
```
#### To get back to original the Docker Console
```bash
eval $(minishift docker-env -u)
```

#### Administration Options
```bash
oc adm ...
```
#### Manual Scale
```bash
oc scale dc nodeapp --replicas=3
```
#### Deployment Configuration 
```bash
oc get dc nodeapp  -o json
oc get dc nodeapp  -o json | jq .status
```
#### Tags Images
```bash
oc tag nodeapp:latest nodeapp:v2
```
#### Remove
```bash
oc tag -d nodeapp:latest
```
