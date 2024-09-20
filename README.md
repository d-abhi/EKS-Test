# EKS-Test

prerequisites :-

kubectl – A command line tool for working with Kubernetes clusters. 
eksctl – A command line tool for working with EKS clusters that automates many individual tasks. 
AWS CLI – A command line tool for working with AWS services, 

1. Create cluster using eksctl

$ eksctl create cluster --name demo-cluster --region us-east-1 --fargate

2. update the kubeconfig.

$ aws eks update-kubeconfig --name demo-cluster --region us-east-1

3. create fargate profile 

$ eksctl create fargateprofile \
    --cluster demo-cluster \
    --region us-east-1 \
    --name alb-sample-app \
    --namespace game-2048