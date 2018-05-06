## Minishift


## Install

# Hypervisor
brew install docker-machine-driver-xhyve
sudo chown root:wheel $(brew --prefix)/opt/docker-machine-driver-xhyve/bin/docker-machine-driver-xhyve
sudo chmod u+s $(brew --prefix)/opt/docker-machine-driver-xhyve/bin/docker-machine-driver-xhyve
# Check installation
brew info --installed docker-machine-driver-xhyve


## Start and Config
# 
minishift start
# One time, configure openshift client
eval $(minishift oc-env)
# Configure Docker Console with Docker Daemon Minishift
eval $(minishift docker-env)
oc login -u system:admin
#
minishift ip
# Stop
eval $(minishift docker-env -u)
minishift stop


## Deploy OpenShift NodeJs App Sample
#
oc new-app https://github.com/openshift/nodejs-ex -l name=myapp
oc logs -f bc/nodejs-ex
oc expose svc/nodejs-ex
minishift openshift service nodejs-ex --in-browser
# Delete all objects of this deployment
oc delete all -l app=nodejs-ex

# Create Applications (https://docs.openshift.com/enterprise/3.0/dev_guide/new_app.html)
# Using Docker build strategy (https://docs.openshift.org/latest/architecture/core_concepts/builds_and_image_streams.html#docker-build)
 1. oc new-app /Users/ualter/Developer/minikube-minishift/nodeApp/ --name=nodeapp --strategy=docker --template=nodejs
 2. oc start-build nodeapp --from-dir=/Users/ualter/Developer/minikube-minishift/nodeApp/ --follow
 3. oc expose svc/nodeapp #expose to a route
 4. minishift openshift service nodeapp --in-browser #open it

# Update the Image (run again...)
oc start-build nodeapp --from-dir=/Users/ualter/Developer/minikube-minishift/nodeApp/ --follow
# Tag the new image
oc tag nodeapp:latest nodeapp:v2

# 
oc rollout history dc/nodeapp 
#
oc logs -f dc/nodeapp 

# Using GIT
# oc new-app https://git-url-project --name=name
# GIT specyfing branch developer
# oc new-app https://github.com/openshift/ruby-hello-world.git#developer

## Others

# List Projects
oc get projects

# List Image Stream
oc describe is

# List all local templates
oc new-app --list

# Delete an App (all its objects)
oc delete all -l app=nodeapp

# Configure Docker Console with Docker Daemon Minishift
eval $(minishift docker-env)
# To get back to original the Docker Console
eval $(minishift docker-env -u)

# Administration options
oc adm

# Manual Scale
oc scale dc nodeapp --replicas=3

# Deployment Configuration 
oc get dc nodeapp  -o json
oc get dc nodeapp  -o json | jq .status


# Tags Images
oc tag nodeapp:latest nodeapp:v2
# Remove
oc tag -d nodeapp:latest
