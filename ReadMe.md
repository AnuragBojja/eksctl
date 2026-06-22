### EKS (Elastic Kubernetes Service)

This module deploys an EKS cluster with the following features:

## installation of ingress controller


1. Installation of OIDC Identity Provider for authentication

install OICD Identity Provider for authentication with OIDC-Provider.yaml file in the current directory. Use the following command to apply the configuration:
```
eksctl utils associate-iam-oidc-provider -f OIDC-provider.yaml --approve
```
* Dont forget to replace the placeholders in the OIDC-provider.yaml file with your actual values before applying the configuration.

or 

install directly using eksctl command:
```
eksctl utils associate-iam-oidc-provider \
    --region <region-code> \
    --cluster <your-cluster-name> \
    --approve
```
---
2. Download an IAM policy for the LBC using one of the following commands:
```
curl -o iam-policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v3.4.0/docs/install/iam_policy.json
```
---
3. Create an IAM policy using the downloaded iam-policy.json file:
```
aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam-policy.json
```
---
4. Create a Kubernetes service account and associate it with the IAM policy created in the previous step. Use the following command to create the service account:
use the following command to create the service account with aws-load-balancer-controller.yaml file in the current directory:
```
kubectl apply -f aws-load-balancer-controller.yaml
```
* dont forget to replace the placeholders in the aws-load-balancer-controller.yaml file with your actual values before applying the configuration.

or

use the following command to create the service account directly:
```
eksctl create iamserviceaccount \
--cluster=<cluster-name> \
--namespace=kube-system \
--name=aws-load-balancer-controller \
--attach-policy-arn=arn:aws:iam::<AWS_ACCOUNT_ID>:policy/AWSLoadBalancerControllerIAMPolicy \
--override-existing-serviceaccounts \
--region <region-code> \
--approve
```