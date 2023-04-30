# aws_jenkins
Deploy Jenkins on AWS with EBS Volume 


EKS Storage with EBS - Elastic Block Store
Step-01: Introduction

    Create IAM Policy for EBS
    Associate IAM Policy to Worker Node IAM Role
    Install EBS CSI Driver

Steps

    Clone the aws_jenkins repository from GitHub by running the following command in the terminal:

    bash

git clone https://github.com/sonulodha/aws_jenkins.git

Change to the aws_jenkins directory:

bash

cd aws_jenkins

Define the policy name by setting the POLICY_NAME variable:

makefile

POLICY_NAME=amazon_ebs_csi_driver_eks

Create the policy using the following command:

bash

aws iam create-policy --policy-name $POLICY_NAME --policy-document file://policy.json

This command creates a policy with the given name ($POLICY_NAME) using the JSON file policy.json.
Get the ARN of the EKS node role by running the following command:

perl

EKS_NODE_ROLE=$(kubectl -n kube-system describe configmap aws-auth|grep rolearn | cut -d'/' -f2)

Get the ARN of the policy using the following command:

bash

POLICY_ARN=$(aws iam list-policies --query "Policies[?PolicyName=='${POLICY_NAME}'].Arn" --output text | cut -f2 -d' ' | tr -d '"')

This command uses the aws iam list-policies command to get the ARN of the policy with the given name ($POLICY_NAME), and then removes the quotes and the space from the output.
Attach the policy to the EKS node role using the following command:

bash

aws iam attach-role-policy --role-name $EKS_NODE_ROLE --policy-arn $POLICY_ARN

This command attaches the policy to the EKS node role with the ARN stored in $EKS_NODE_ROLE.
