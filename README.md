# aws_jenkins
Deploy Jenkins on AWS with EBS Volume 


EKS Storage with EBS - Elastic Block Store
Step-01: Introduction

    Create IAM Policy for EBS
    Associate IAM Policy to Worker Node IAM Role
    Install EBS CSI Driver

git clone https://github.com/sonulodha/aws_jenkins.git

cd aws_jenkins

POLICY_NAME=amazon_ebs_csi_driver_eks

aws iam create-policy --policy-name $POLICY_NAME --policy-document file://policy.json

EKS_NODE_ROLE=$(kubectl -n kube-system describe configmap aws-auth|grep rolearn | cut -d'/' -f2)

POLICY_ARN=$(aws iam list-policies --query "Policies[?PolicyName=='${POLICY_NAME}'].Arn" --output text | cut -f2 -d' ' | tr -d '"')

aws iam attach-role-policy --role-name $EKS_NODE_ROLE --policy-arn $POLICY_ARN



