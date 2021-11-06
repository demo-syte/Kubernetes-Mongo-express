# Kubernetes-Mongo-express setup
A simple Mongo-express Set-up on your local cluster - minikube

Final Config looks like below -

Kubernetes$ # kubectl get service
NAME                    TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
kubernetes              ClusterIP      10.96.0.1        <none>        443/TCP          7d11h
mongo-express-service   LoadBalancer   10.107.179.102   <pending>     8081:30000/TCP   35m
mongodb-service         ClusterIP      10.98.203.153    <none>        27017/TCP        36m
Kubernetes$ # minikube service mongo-express-service
|-----------|-----------------------|-------------|-----------------------------|
| NAMESPACE |         NAME          | TARGET PORT |             URL             |
|-----------|-----------------------|-------------|-----------------------------|
| default   | mongo-express-service |        8081 | http://192.168.99.100:30000 |
|-----------|-----------------------|-------------|-----------------------------|
🎉  Opening service default/mongo-express-service in default browser...

![mongo-expres service](https://user-images.githubusercontent.com/78690371/140602832-dc2f35f4-aaf0-4c4b-92b5-f6cce3b391e3.png)


# Prerequisite

Make sure Minikube and kubectl are installed.

kubectl installation
Minikube installation

General concept:-

    A Pod is the smallest execution unit in Kubernetes, and an abstraction over containers. A container is a lightweight software that contains all the necessary tools, libraries and code to run an application.
        kubectl exec -it <pod_name> -- /bin/bash to get a bash shell in the container.
        kubectl logs <pod_name> to show information logged by a pod.

    A Deployment is a resource to describe how a ReplicaSet and Pod should behave.
        kubectl create deploy <deployment_name> --image=<image_name> to create a basic deployment with minimal configurations.
        kubectl edit deploy <deployment_name> to edit the configuration file of a deployment.
        kubectl delete deploy <deployment_name> to delete a deployment.

    A ReplicaSet aims to maintain a stable set of Pods running at any given time. The number of Pods maintained is determined in the configuration file of a deployment.

    A Service exposes an application running on a set of Pods as a network service.

    A ConfigMap is used to store non-sensitive data in key-value pairs, and can be consumed as environment variables, command-line arguments, or as configuration files.

    A Secret is similar to a ConfigMap, but more towards storing sensitive data, such as passwords, OAuth tokens, and ssh keys.
        echo -n '<password>' | base64 to generate a base64 encoded string to be used as password in Secret, though a better hashing algorithm should be used.
  

    An Ingress manages external access to services in a cluster. It provides load balancing, SSL termination and name-based virtual hosting. An Ingress consists of an Ingress Controller and Ingress Resource. An Ingress Controller reads information from an Ingress Resource then evaluates all the rules and manages redirections.

Getting Started

1 - Start Minikube, this will start your local cluster

    minikube start 
    

2 - Create a Secret, which is used to store sensitive information. The Secret will contain the username and password for MongoDB.

    kubectl apply -f mongo-secret.yaml to create the Secret called mongodb-secret.
    kubectl delete -f mongo-secret.yaml to delete the Secret.
    kubectl get secret -n mongodb-namespace to list all Secrets.

3 - Create a Deployment, which is used to manage Pods and ReplicaSets, and a Service, which defines a policy to access a set of Pods. The Deployment will contain a MongoDB Pod, and will use the username and password from Secret as the database credentials. The Service defined is an internal service, which is inaccessible outside of the Kubernetes cluster. It functions to enable other Pods within the cluster to communicate with the MongoDB Pod.

    kubectl apply -f mongo.yml to create the Service called mongodb-service, and Deployment called mongodb-deployment.
    kubectl delete -f mongo.yml to delete the Service and Deployment.
    kubectl get svc 
    kubectl get deploy
    

4 - Create a ConfigMap, which is used to store non-confidential information in key-value pairs. The ConfigMap will contain the mongo database url.

    kubectl apply -f mongo-configmap.yaml to create the Configmap called mongodb-configmap.
    kubectl delete -f mongo-configmap.yaml to delete the Configmap.
    kubectl get cm  

5 - Create another Deployment and Service. The Deployment will contain a Mongo-Express Pod, which is a web-based interface to manage MongoDB databases. It will use the username and password from Secret, and the database url from ConfigMap to access the MongoDB internal Service defined in 4. The Service defined in 6 could be either an external or internal service. If it's an external service, it would allow external request to communicate with the Pods in 6. However, since an Ingress will be defined in 7, this Service will be an internal service, which would define the policy to access the Mongo-Express Pod.

    kubectl apply -f mongo-express.yaml to create the Service called mongodb-express-service, and Deployment called mongodb-express-deployment.
    kubectl delete -f mongo-express.yaml to delete the Service and Deployment.

