# To deploy an Application on Amazon Elastic Kubernetes Service Cluster using ArgoCD tool

## Create IAM Role for AWS EKS Cluster

Trusted Entity Type - EKS-Cluster

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/3cbf3bfa-b80c-4e98-8712-57a60b1bf46f)

## Create IAM Role for EKS Nodegroup

Trusted Entity Type - EC2

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/dbe5c164-81fa-4407-8545-0fa42b066054)

## Create EKS Cluster

Navigate to AWS EKS Console - Create Cluster and assign EKS-Cluster IAM Role

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/610aa971-2e46-4d65-826e-263f522baf69)

Networking settings - Select your all subnets for High availability and select the security group.

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/c7b0003d-03a6-4abe-855a-5b3380749aae)

Cluster endpoint access would be a Public access

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/1b4dee57-33dc-4d1a-808c-428c8e1d5d5d)

I leave as default for other configurations and next to create a Cluster. It will take morethan 10 minutes to be Active state.

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/7f1565c4-c00d-4e5a-af01-1e49f0f1b0ef)

Now, EKS Cluster has been created.

## Create a Node group for EKS Cluster

After creation of EKS Cluster, Select your cluster and choose Add Node group

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/ef24d51b-b319-4463-a488-e2fca669b179)

To create a Node group with IAM Role which has been created first step.

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/df38f071-f448-4591-ba70-643d20d9765d)

Click next - to configure the Compute and scaling

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/4b0867ef-d524-4f66-bdee-c2df7ddd2ad1)

I leave scaling configuration as default,

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/6cb83ddc-53b1-428b-8814-bf5e05e9ac27)

Select your subnets and create a node group.

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/0926e20b-2d70-492f-a954-624351df6176)

This (Worker Node) will take some times to up and running.

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/6ff1b81e-aaea-4192-875e-bd5494caaf09)

## To Setup a GitOps ArgoCD to deploy containers on EKS

To open the AWS Cloudshell with new tab and set the EKS context by following command.

    aws eks update-kubeconfig --name EKS_CLUSTER_NAME --region CLUSTER_REGION

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/37e439fc-38b6-43e9-9b98-db32b27190d3)

To check the nodes

    kubectl get nodes

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/5c9f5b98-38b8-47c9-9452-4ca8f99ee721)

Lets check the pods - You wont see anything

    kubectl get pods

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/ebb678ea-46a5-4631-8970-893bb1bdba8a)

Now we have to Install ArgoCD

All the components could be installed using a manifest file which is provided by ArgoCD project - To use below commands to acheive.

    kubectl create namespace argocd
    kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.4.7/manifests/install.yaml

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/717858a4-1120-4b97-b327-78ff5e2a2c81)

By default, ArgoCD is not publicly exposed. So we have to create a Load balancer to make it available publicly.

    kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/2f22735f-9f39-42c7-9a37-23d937650025)

As a result, One load balancer has been created. If you access the DNS URL of the Load balancer then you can access the ArgoCD login console.

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/a6edbe05-d54d-42a7-8311-a51e839ca61c)

Below command will export the hostname of the Load balancer to the variable called "ARGOCD_SERVER" - It may use for programmatic access.

    export ARGOCD_SERVER=`kubectl get svc argocd-server -n argocd -o json | jq --raw-output '.status.loadBalancer.ingress[0].hostname'`

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/966e72de-a404-4d0a-8e79-999a36379fb3)

## To login ArgoCD

In order to access the ArgoCD web UI,you have to extract the password for the initial admin user of ArgoCD, decode it from base64 encoding, and store it in an environment variable named ARGO_PWD.

    export ARGO_PWD=`kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d`
    echo $ARGO_PWD

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/3dae25c9-4bd7-4bce-9ed4-62f103a3b4f1)

Just copy the password and access the ArgoCD web UI to login

    username - admin
    password - -yVw5Eha9LfMfMnS

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/9720ae9d-33c4-4ffe-b8ef-abad6070e9b3)

After login, click on the settings to configure repositories

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/74f62dfe-5183-40f1-8557-bfbc193fffd7)

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/0ec5163a-0216-436d-8e54-29baf0ae2663)

Choose "Connect repo using HTTPS" and Provide the Git repo URL and I leave others as blank.

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/9a1392f4-a2ed-46dd-9a8a-48a6893eae00)

After connect the repo, you can see the successful message.

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/ff7d22d4-193d-47c0-812d-41e07b44307f)

Click on the "Manage your application" to create new app.

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/0aa1776b-9f8a-43c3-a30c-2fce4b224e99)

Configure as below in images

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/14e568c8-5de1-4e06-88e6-95eb0c168581)

Source would be 

Path -> manifests -> In repo where my yaml files located. Please change the path as per your needs.

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/42e9f8c2-0ef3-4f62-a149-32a535164866)

Destination would be

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/1bb0fed8-45d4-43a7-9f19-0f11ebe3c2e3)

Then create a app. Once app has been created then you can see the status of the app.

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/dd414424-9bba-4a98-84ea-d57cd7462b94)

Just click on the created app to select the details.

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/9f4b2f84-0fb9-4633-8640-6800a20cb3e2)

Finally copy the hostname to access the app.

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/20205b81-ac0d-41a0-a056-00c3fa62a57c)

I have changed my docker image in my repo which contains Netflix clone page.

This is my image name which is stored in my docker hub

    latchudevops/netflix
    docker push latchudevops/netflix:latest

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/0a0e7a5d-4ea6-44a0-90da-b06c816d29c7)

You can also change image version or new image in the yaml file - Then automatically sync with ArgoCD and deploy the new image on the EKS worker node.

Perfect! Please clean up the resources.
