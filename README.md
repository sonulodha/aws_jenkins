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

    POLICY_NAME=amazon_ebs_csi_driver_eks

Create the policy using the following command:

    aws iam create-policy --policy-name $POLICY_NAME --policy-document file://policy.json

Get the ARN of the EKS node role by running the following command:

    EKS_NODE_ROLE=$(kubectl -n kube-system describe configmap aws-auth|grep rolearn | cut -d'/' -f2)

Get the ARN of the policy using the following command:

    POLICY_ARN=$(aws iam list-policies --query "Policies[?PolicyName=='${POLICY_NAME}'].Arn" --output text | cut -f2 -d' ' | tr -d '"')

Attach the policy to the EKS node role using the following command:

    aws iam attach-role-policy --role-name $EKS_NODE_ROLE --policy-arn $POLICY_ARN

Deploy Amazon EBS CSI Driver
    
    kubectl apply -k "github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable/?ref=master"

Verify ebs-csi pods running
    
    kubectl get pods -n kube-system
 
 Create namespace for jenkins
 
    kubectl create namespace jenkins   
    
Create ebs storage class 
    
    kubectl apply -f storageclass.yaml -n jenkins
    
Create PersistentVolumeClaim 
    
    kubectl apply -f PersistentVolumeClaim.yml -n jenkins
    
Create jenkins deployment 

    kubectl apply -f deployment.yml -n jenkins 
    kubectl -n jenkins get pod
 
Expose jenkins deploy as LoadBalancer Service

    kubectl apply -f service.yml -n jenkins
    
    kubectl -n jenkins get svc 
