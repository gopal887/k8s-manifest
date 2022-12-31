
what is cicd pipe line > link https://www.guru99.com/ci-cd-pipeline.html


---------------------------------------------------------------------------
 # open-mrs #

**Lets find how  Game of life works and  what are steps need to run application.**
**steps:-**
- Find all the manual steps .
- Check on which port its execute
- create a ec2 instance and install Docker also give user permissions to docker.

 Mannual Features about docker overview link > https://www.docker.com/resources/what-container/
docker install :
- Lets create a linux vm in any cloud of your choice.

# Installation on AWS #
- Create a key pair (ensure you select pem format)
- Create a security group and open everything
- To login into linux machine
ssh -i <path to pem file> username@ipaddress
cd ~/Downloads
ssh -i .\fordocker.pem ubuntu@ public ip 
- After login into the linux instanc
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh

- sudo usermod -aG docker <username>
- exit and relogin
- docker info
This command should not give any errors
---
- For docker commands use cheatsheet Refer Here for docker cheatsheet.
- Lets try to run the following container

*Dockerfile Creation*

Openmrs Core:
FROM maven:3-jdk-8 as mvn
LABEL author='venu'
RUN git clone https://github.com/openmrs/openmrs-core.git && cd openmrs-core && mvn clean package
 
FROM tomcat:8
LABEL author='Khaja'
COPY --from=mvn /openmrs-core/webapp/target/openmrs.war /usr/local/tomcat/webapps/openmrs.war
EXPOSE 8080
CMD ["catalina.sh", "run"]

- now build the image and push the docker image to docker hub

docker image build -t venu1212/open-mrs:UV

docker push venu1212/open-mrs:UV

- How to run openmrscore on docker
docker container run --name openmrs -d -p 8080:8080 shaikkhajaibrahim/openmrs-core:2.4.0

*http:<ip>:8080/openmrs*

# install AKS cluster
 https://stacksimplify.com/azure-aks/azure-kubernetes-service-namespaces-imperative/

Quickstart: Deploy an Azure Kubernetes Service cluster using the Azure CLI (https://learn.microsoft.com/en-us/azure/aks/learn/quick-kubernetes-deploy-cli) 

- Azure Kubernetes Service (AKS) is a managed Kubernetes service that lets you quickly deploy and manage clusters. In this quickstart, you will:

- Deploy an AKS cluster using the Azure CLI.
- Verify Microsoft.OperationsManagement and Microsoft.OperationalInsights providers are registered on your subscription.
- These are Azure resource providers required to support Container insights.
- To check the registration status, run the following commands:

Azure CLI (command)
- az provider register --namespace Microsoft OperationsManagement

*Create a resource group*

An Azure resource group is a logical group in which Azure resources are deployed and managed. When you create a resource group, you are prompted to specify a location. This location is:

* The storage location of your resource group metadata. 
 * Where your resources will run in Azure if you don't specify another region during resource creation.
 * The following example creates a resource group named myResourceGroup in the eastus location.
 * Create a resource group using the az group create (command)

 az group create --name myResourceGroup
 --location eastus
 * Create AKS cluster (command)

az aks create -g myResourceGroup -n myAKSCluster --enable-managed-identity --node-count 2 --enable-addons monitoring --enable-msi-auth-for-monitoring  --generate-ssh-keys

* Install kubectl locally using the az aks install-cli command:
az aks install-cli

* Configure kubectl to connect to your Kubernetes cluster using the az aks get-credentials command. The following command:

az aks get-credentials --resource-group myResourceGroup --name myAKSCluster

## K8s Specifications ## 

*Pod specification for Openmrscore*

- Deployment of the Openmrs will look like
```yaml


---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: open-mrs-deploy
spec:
  minReadySeconds: 10
  replicas: 4
  selector:
    matchLabels:
      app: openmrs
  strategy: 
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
  template:
    metadata:
      name: open-mrs-deploy
      labels:
        app: openmrs
    spec:
      containers:
        - name: open-mrs-deploy
          image: venu1212/open-mrs:UV
          ports:
            - containerPort: 8080
              protocol: TCP


service of the openmrs will look like

```yaml
---
apiVersion: v1
kind: Service
metadata: 
  name: open-mrs-lb
spec:
  type: LoadBalancer
  selector:
    app: openmrs
  ports:
    - name: webport
      port: 35000
      targetPort: 8080

Deploy the application using the kubectl apply command and specify the name of your YAML manifest: (commands)
kubectl apply -f /.openmrs-deploy.yaml
kubectl apply -f /. openmrs-service.yaml

 kubectl get svc
 ```
![Alt text](Screenshot%20(25).png)

* you can get an External Ip, copy the extenal IP 
 * now search on google
 ```
 http://<external-ip>:<port>/openmrs
 ```
![Alt text](Screenshot%20(23).png)




