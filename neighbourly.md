# Neighbourly App

This are my notes for the submission of a project I already did, but with different source code and slightly different instructions.

| Resource|  Name|
| - | - |
| Resource group | neighbourly |
| Function app name | ndfun |
| Cosmos db account | nd-db |
| Mongo DB name | nd-db-mongo |


### 1.1 Create resource group
> az group create --name "neighbourly" --location "east us"

> az configure --defaults group=neighbourly

### 1.2 Create storage account

> az storage account create \
  --kind StorageV2 \
  --location centralus \
  --name s2ndproject


### 1.3 Create an Azure Function App

I used the portal "Function App"

Choose python 3.8 and Linux.

### 1.4 Cosmos DB account

I used the portal. Choose Mongo API

### 1.5 Create Mongo DB database in the cosmos account

> az cosmosdb mongodb database create \
    -a nd-db \
    -n nd-db-mongo

> az cosmosdb mongodb collection create \
    -a nd-db \
    -d nd-db-mongo \
    -n advertisements --shard '_id'


    shard, what is it?

> az cosmosdb mongodb collection create \
    -a nd-db \
    -d nd-db-mongo \
    -n posts --shard '_id'

### 1.6 Get the connection string for the database

 + az cosmosdb keys list -n nd-db  -g neighbourly --type connection-strings

mongodb://nd-db:@nd-db.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false&maxIdleTimeMS=120000&appName=@nd-db@

"mongodb://nd-db:dJhfzUf1gYswJG9HYgl8sddJTb0Rc8GaurrWRNOAfMDSAwKr0JjsdM7dUO514KjgfNLRjtZctT8TuhAQqwV9yQ==@nd-db.mongo.cosmos.azure.com:10255/?ssl=false&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@nd-db@"


### 1.7 import existing data into db
Connect using the cli

>mongo nd-db.mongo.cosmos.azure.com:10255 -u nd-db -p dJhfzUf1gYswJG9HYgl8sddJTb0Rc8GaurrWRNOAfMDSAwKr0JjsdM7dUO514KjgfNLRjtZctT8TuhAQqwV9yQ== --ssl --sslAllowInvalidCertificates

switch to our database
> use nd-db-mongo

import documents from the sample data files .json
> db.collection.insertMany([ {}, {}, {} ])

### 2.0 Test azure function locally

Modify the connection string and database fields on the init files inside the direcotry of NeighborlyAPI

> cd NeighborlyAPI

Inside the virtual env activated, install requirements and run
> func start

It is now running in localhost:7071

Could not connect to mongodb
a password for the connection string is required

nd-db.mongo.cosmos.azure.com:10255: [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1108)

client = pymongo.MongoClient(uri, ssl_cert_reqs=ssl.CERT_NONE)

I disabled SSL only for one endpoint (getAdvertisements) to test locally. When I deployed the others are working (getPosts) with the default config even though https is not enabled. I tried fetching a single advertisement with the id format and I got null. Maybe the format was wrong, I should try again with postman. And, locally without SSL, maybe.

I deployed using VS Code to my existing function app.
And I already tested those two endpoints with the front end app running on localhost.

### Containerization

Create the Docker file, build, and test run.


In the azure portal create an instance of a private Azure Container Registry

Tutorial for pushing to azure container registry from visual studio code
https://code.visualstudio.com/docs/containers/app-service

In the portal, under the registry service, enable admin user under Access Keys.

And, run it. Repositories / image / tag / run (execute)

container : neighborly-frontend

Image: ndneigh.azurecr.io/neighborly-front:latest

https://docs.microsoft.com/en-us/azure/container-instances/container-instances-using-azure-container-registry

### test kubernetes locally

Set the context of kubeconfig to docker desktop

To test with kubernetes on docker desktop, you have to login to docker and create a secret for kubernetes with the azure container registry credentials.

Tutorial here:
https://thorsten-hans.com/how-to-use-private-azure-container-registry-with-kubernetes

        ---
        kind: Service
        apiVersion: v1
        metadata:
        name: neighborly-frontend4
        spec:
        type: LoadBalancer
        selector:
            app: neighborly-frontend4
        ports:
            - name: http
            protocol: TCP
            port: 80
            targetPort: 5000
        ---
        apiVersion: apps/v1
        kind: Deployment
        metadata:
        name: neighborly-frontend4
        spec:
        replicas: 2
        selector:
            matchLabels:
            app: neighborly-frontend4
        template:
            metadata:
            labels:
                app: neighborly-frontend4
            spec:
            containers:
            - name: neighborly-frontend4
                image: ndneigh.azurecr.io/neighborly-front:latest
                ports:
                - containerPort: 5000
                hostPort: 5000
                protocol: TCP
            imagePullSecrets:
            - name: azcrlogin

In this case the secret is called azcrlogin, and has to be specified.

### Create a cluster in azure

az aks create --resource-group neighbourly --name myAKSCluster --node-count 1 --enable-addons monitoring --generate-ssh-keys

az aks get-credentials --resource-group neighbourly --name myAKSCluster

https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough

I could just deploy the same way and use the IP of the load balancer, but to use a domain , I will use an ingress.

So, we need to change the load balancer to a cluster IP, and add an ingress.

Tutorial here.
https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-on-digitalocean-kubernetes-using-helm


Domain here:

http://neigh.danielabg.com/

Note: the browser still displays the site is not safe despite it having an SSL certificate.
This is because in the templates files the links are referred to http instead of https. So those files or images make the warning. TODO: update the routes. But it would break my other deployment that doesn't have SSL.

### TODO: Tutorial to implement rolling update

https://kubernetes.io/docs/tutorials/kubernetes-basics/update/update-intro/