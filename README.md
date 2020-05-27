## Create a Cluster
view the cluster details
`kubectl cluster-info`

 shows all nodes
`kubectl get nodes`

## Deploy an App
Deploy app
`kubectl create deployment name --image=XXXX`
image = app image location(full repository url)

list deployments
`kubectl getdeplyments`
connect clouster-wide, private network
`kubectl proxy`

second terminal window
ensure proxy started
connetct API server through the proxy
`curl http://localhost:8001/version`

store POD_NAME in the environment variable.
`export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')`

`echo Name of the Pod: $POD_NAME`

## Explore Your App
" A Pod is a Kubernetes abstraction that represents a group of one or more application containers"
"Pods are the atomic unit on the Kubernetes platform"

"A Pod always runs on a Node"
"Each Node is managed by the Master"

`kubectl get pods`

details about the Pod’s container
`kubectl describe pods`


`curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME/proxy/`

Anything that the application would normally send to STDOUT becomes logs for the container within the Pod
`kubectl logs $POD_NAME`

execute commands directly on the container
`kubectl exec $POD_NAME env`

 bash session in the Pod’s container
`kubectl exec -ti $POD_NAME bash`

To close your container connection
`exit`

## Using a Service to Expose Your App
list the current Services
`kubectl get services`

To create a new service and expose
`kubectl expose deployment/kubernetes-bootcamp --type="NodePort" --port 8080`

Create an environment variable called NODE_PORT
`export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')`

`echo NODE_PORT=$NODE_PORT`
app is exposed outside of the cluster

 label to query our list of Pods
`kubectl get pods -l app=kubernetes-bootcamp`
same to list the existing services
`kubectl get services -l app=kubernetes-bootcamp`
To apply a new label 
`kubectl label pod $POD_NAME run=v1`

list of pods using the new label
`kubectl get pods -l app=v1`
To delete Services
`kubectl delete service -l app=kubernetes-bootcamp`
Confirm that the service is gone
`kubectl get services`

## Running Multiple Instances of Your App
To see the ReplicaSet
`kubectl get rs`

the name of the ReplicaSet is always formatted as
`[DEPLOYMENT-NAME]-[RANDOM-STRING]`

scale the Deployment to 4 replicas
`kubectl scale deployments/kubernetes-bootcamp --replicas=4`
number of Pods
`kubectl get pods -o wide`

there are 4 replicas
`kubectl describe deployments/kubernetes-bootcamp`

`kubectl describe services/kubernetes-bootcamp`

To find out the exposed IP and Port
`kubectl describe services/kubernetes-bootcamp`

Create an environment variable called NODE_PORT
`export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')`
`echo NODE_PORT=$NODE_PORT`

To scale down the Service to 2 replicas
`kubectl scale deployments/kubernetes-bootcamp --replicas=2`

List the Deployments to check
`kubectl get deployments`
`kubectl get pods -o wide`

## Updating Your App

To update the image of the application to version 2  

`kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=jocatalin/kubernetes-bootcamp:v2`


check that the App is running
`kubectl describe services/kubernetes-bootcamp`

Create an environment variable called NODE_PORT  
`export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')`

`echo NODE_PORT=$NODE_PORT`


The update can be confirmed  
`kubectl rollout status deployments/kubernetes-bootcamp`  

deploy image tagged as v10  
`kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=gcr.io/google-samples/kubernetes-bootcamp:v10`

the Pods should give more insights  
`kubectl describe pods`
There is no image called v10 in the repository   

roll back to our previously working version  
`kubectl rollout undo deployments/kubernetes-bootcamp`  

Check again the image deployed  
`kubectl describe pods`  

