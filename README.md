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

4. Deploy the deployment, service and Ingress

$ kubectl apply -f https://raw.githubusercontent.com/d-abhi/EKS-Test/main/2048_full.yml


5. Approve IAM OIDC provider ((ALB controller need access to ALB))  
     ALB controller -> Pod -> Access to AWS services such as ALB

$ eksctl utils associate-iam-oidc-provider --cluster $cluster_name --approve


6. Download IAM policy : - 

$ curl -O https://raw.githubusercontent.com/d-abhi/EKS-Test/main/iam_policy.json

7. Create IAM Policy

$ aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json

8. Attaching the Role to service account of the pod.

$ 
eksctl create iamserviceaccount \
  --cluster=demo-cluster \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::471112994686:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve

9. Deploy ALB controller 

ingress controller -> ingress-2048 -> Load balancer -> target group , port


$ helm repo add eks https://aws.github.io/eks-charts

$ helm repo update eks

$ helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system \
  --set clusterName=demo-cluster \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=us-east-1 \
  --set vpcId=vpc-008f7b3946416d812

10 check at least 2 replica of ALB

$  kubectl get deployment -n kube-system aws-load-balancer-controller
$ kubectl get deploy -n kube-system

11. checking of address. 

kubectl get ingress -n game-2048
kubectl get pods -n game-2048 
kubectl get svc -n game-2048










 

