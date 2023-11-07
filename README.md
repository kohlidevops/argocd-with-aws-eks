# argocd-with-aws-eks

# Create IAM Role for AWS EKS Cluster

Trusted Entity Type - EKS-Cluster

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/3cbf3bfa-b80c-4e98-8712-57a60b1bf46f)

# Create IAM Role for EKS Nodegroup

Trusted Entity Type - EC2

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/dbe5c164-81fa-4407-8545-0fa42b066054)

# Create EKS Cluster

Navigate to AWS EKS Console - Create Cluster and assign EKS-Cluster IAM Role

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/610aa971-2e46-4d65-826e-263f522baf69)

Networking settings - Select your all subnets for High availability and select the security group.

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/c7b0003d-03a6-4abe-855a-5b3380749aae)

Cluster endpoint access would be a Public access

![image](https://github.com/kohlidevops/argocd-with-aws-eks/assets/100069489/1b4dee57-33dc-4d1a-808c-428c8e1d5d5d)

I leave as default for other configurations and next to create a Cluster. It will take morethan 10 minutes to be Active state.

Now, EKS Cluster has been created.

# Create a Node group for EKS Cluster

