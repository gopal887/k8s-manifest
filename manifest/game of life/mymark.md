# Game of Life

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)
**Lets find how  Game of life works and  what are steps need to run application.**
**steps:-**

- Find all the manual steps .
- Check on which port its execute
- create a ec2 instance and install Docker also give user permissions to docker
 

## Mannual Features 
DockerFile
```
FROM tomcat:8-jdk8
EXPOSE 8080
ADD https://referenceapplicationskhaja.s3.us-west-2.amazonaws.com/gameoflife.war /usr/local/tomcat/webapps/gameoflife.war
CMD ["catalina.sh", "run"]
```
*After build Dockerfile*
- Execute the command
```sh
docker image build -t venu1212/gameoflife:DON .
```
- containerized the image 
```sh
docker container run -d -P venu1212/gameoflife:DON
```
- push the image from Docker to  Docker Registry

REFER here for  docker Registry(https://hub.docker.com/search?image_filter=official&q=)

```
docker push eanjaneyulu/gameoflife:1.1
```
# INSTALL EKS Cluster

>Install the AWS CLI into ec2 instance  
> configure the  IAM User
> kubectl  install 
> EKSCTL
- To set up eks cluster we would be using terraform  as through we using k8's cluster
> Install terraform on ec2
> Creating EKS cluster from terraform 
- after installing the terraform use the following commands 
```
git clone https://github.com/hashicorp/learn-terraform-provision-eks-cluster
cd learn-terraform-provision-eks-cluster
terraform init
terraform apply -auto-approve
aws eks --region $(terraform output -raw region) update-kubeconfig \
    --name $(terraform output -raw cluster_name) 
```
# write a k8's  Manifest for Deployment
- create a Deployment manifest for game of life
```yaml
---
apiVersion: apps/v1
kind: Deployment
metadata: 
  name: gameof
spec:
  minReadySeconds: 5
  replicas: 2
  selector:
    matchLabels:
      app: gameoflife
      ver: "1.1"
  strategy:
    type: Recreate
  template:
    metadata:
      name: gameof
      labels:
        app: gameoflife
        ver: "1.1"
    spec:
      containers:
        - name: mygameofsample
          image: venu1212/gameoflife:DON
          ports:
            - containerPort: 8080
              protocol: TCP
```


* now create (or) apply the k8's mainfest in cluster by using this commands
```
kubectl apply -f <filename>.yaml
```
* for check the pods are running or not use this commands    
```
kubectl get po
```
### now create a service to expose outside:-


* now lets apply same following to the service manifest also

```yaml
---
apiVersion: v1
kind: Service
metadata: 
  name: mysvc
spec:
  type: loadBalancer
  selector:
     app: gameoflife
  ports:
    - mame: myporst
      port: 35000
      targetPort: 8080
```
* execute the commands
```
 kubectl apply -f <filename>.yaml
 kubectl get svc
 ```
* you can get an External Ip, copy the extenal IP 
 * now search on google
 ```
 http://<external-ip>:<port>/gameoflife
 ```
 ![preview](https://i0.wp.com/directdevops.blog/wp-content/uploads/2022/11/container26.png?w=800&ssl=1)
 
