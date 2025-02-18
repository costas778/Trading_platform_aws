

## Scenario:
The company ABC Limited operates within the Fin-tech industry offers its own Trading platform as
a service.

This platform can be purchased by clients as a whole solution that has all the necessary
functionality to buy and sell commodities.

## The platform consists of the following:
1. The front-ends talks to the back end services over Rest API.
2. The back-end talks to an Axon Server and a Database layer.
3. There is a total of 20 services that make up the whole platform.


## Task Objectives
1. How would you setup your Infrastructure?
2. How would you setup your infrastructure monitoring?
3. How would you setup your log monitoring?
4. How would you setup up your CI/CD workflow? What tools would you use and how?
5. How would you handle scaling?
6. How would you make sure that you can deploy this infrastructure on different AWS Accounts,
whilst minimising human errors and repetition?

## Environment Configuration

# Your setup will be similar to the following:

#tree
```├── Bash.txt
├── README.md
├── backups
│   ├── backup_20250214_132556.tar.gz
│   └── pre_restore_20250214_132726.tar.gz
├── buildspec.yml
├── config
├── create-patches.sh
├── deploy-services.sh
├── deployment
│   ├── build.sh
│   ├── build.sh.bak
│   ├── deploy.sh
│   ├── deploy.sh.bak
│   └── pipeline.yml
├── docker
│   ├── axon-server
│   │   └── Dockerfile
│   ├── backend
│   │   └── Dockerfile
│   └── frontend
│       └── Dockerfile
├── docs
│   ├── architecture
│   ├── disaster-recovery
│   │   └── dr-plan.md
│   └── runbooks
│       └── operations.md
├── errors.txt
├── final.txt
├── infrastructure
│   ├── database.sh
│   ├── database.sh.bak
│   ├── eks.sh
│   ├── eks.sh.bak
│   └── terraform
│       ├── environments
│       │   ├── dev
│       │   │   ├── backend.tf
│       │   │   ├── eks.tfplan
│       │   │   ├── locals.tf
│       │   │   ├── main.tf
│       │   │   ├── main.tf.bak
│       │   │   ├── terraform.tfvars
│       │   │   ├── tfplan
│       │   │   └── variables.tf
│       │   ├── main.tf
│       │   ├── prod
│       │   │   ├── main.tf
│       │   │   ├── main.tf.bak
│       │   │   ├── terraform.tfvars
│       │   │   └── variables.tf
│       │   └── staging
│       │       ├── main.tf
│       │       ├── main.tf.bak
│       │       ├── terraform.tfvars
│       │       └── variables.tf
│       ├── main.tf
│       └── modules
│           ├── alb
│           │   ├── main.tf
│           │   ├── outputs.tf
│           │   └── variables.tf
│           ├── api-gateway
│           │   ├── main.tf
│           │   ├── outputs.tf
│           │   └── variables.tf
│           ├── cache
│           │   ├── main.tf
│           │   ├── outputs.tf
│           │   └── variables.tf
│           ├── cloudwatch
│           │   ├── main.tf
│           │   ├── outputs.tf
│           │   └── variables.tf
│           ├── ecr
│           │   ├── main.tf
│           │   ├── outputs.tf
│           │   └── variables.tf
│           ├── eks
│           │   ├── main.tf
│           │   ├── outputs.tf
│           │   └── variables.tf
│           ├── iam
│           │   ├── main.tf
│           │   ├── outputs.tf
│           │   └── variables.tf
│           ├── kms
│           │   ├── main.tf
│           │   ├── outputs.tf
│           │   └── variables.tf
│           ├── message-queue
│           │   ├── main.tf
│           │   ├── outputs.tf
│           │   └── variables.tf
│           ├── networking
│           │   ├── main.tf
│           │   ├── outputs.tf
│           │   └── variables.tf
│           ├── rds
│           │   ├── main.tf
│           │   ├── outputs.tf
│           │   └── variables.tf
│           ├── s3
│           │   ├── main.tf
│           │   ├── outputs.tf
│           │   └── variables.tf
│           ├── secrets
│           │   ├── main.tf
│           │   ├── outputs.tf
│           │   └── variables.tf
│           ├── security_groups
│           │   ├── main.tf
│           │   ├── outputs.tf
│           │   └── variables.tf
│           └── vpc
│               ├── main.tf
│               ├── outputs.tf
│               └── variables.tf
├── install-prerequisites.sh
├── k8s
│   ├── base
│   │   ├── axon-server
│   │   │   ├── deployment.yaml
│   │   │   └── service.yaml
│   │   ├── backend-services
│   │   │   ├── deployment.yaml
│   │   │   └── service.yaml
│   │   ├── database
│   │   │   └── persistent-volume.yaml
│   │   ├── frontend
│   │   │   ├── deployment.yaml
│   │   │   └── service.yaml
│   │   └── trading-services
│   │       ├── api-gateway
│   │       │   ├── deployment.yaml
│   │       │   └── service.yaml
│   │       ├── audit
│   │       │   ├── deployment.yaml
│   │       │   └── service.yaml
│   │       ├── authentication
│   │       │   ├── deployment.yaml
│   │       │   └── service.yaml
│   │       ├── authorization
│   │       │   ├── deployment.yaml
│   │       │   └── service.yaml
│   │       ├── cache
│   │       │   ├── deployment.yaml
│   │       │   └── service.yaml
│   │       ├── compliance
│   │       │   ├── deployment.yaml
│   │       │   └── service.yaml
│   │       ├── logging
│   │       │   ├── deployment.yaml
│   │       │   └── service.yaml
│   │       ├── market-data
│   │       │   ├── deployment.yaml
│   │       │   └── service.yaml
│   │       ├── message-queue
│   │       │   ├── deployment.yaml
│   │       │   └── service.yaml
│   │       ├── notification
│   │       │   ├── deployment.yaml
│   │       │   └── service.yaml
│   │       ├── order-management
│   │       │   ├── deployment.yaml
│   │       │   └── service.yaml
│   │       ├── portfolio-management
│   │       │   ├── deployment.yaml
│   │       │   └── service.yaml
│   │       ├── position-management
│   │       │   ├── deployment.yaml
│   │       │   └── service.yaml
│   │       ├── price-feed
│   │       │   ├── deployment.yaml
│   │       │   └── service.yaml
│   │       ├── quote-service
│   │       │   ├── deployment.yaml
│   │       │   └── service.yaml
│   │       ├── reporting
│   │       │   ├── deployment.yaml
│   │       │   └── service.yaml
│   │       ├── risk-management
│   │       │   ├── deployment.yaml
│   │       │   └── service.yaml
│   │       ├── settlement
│   │       │   ├── deployment.yaml
│   │       │   └── service.yaml
│   │       ├── trade-execution
│   │       │   ├── deployment.yaml
│   │       │   └── service.yaml
│   │       └── user-management
│   │           ├── deployment.yaml
│   │           └── service.yaml
│   └── overlays
│       ├── dev
│       │   └── kustomization.yaml
│       ├── prod
│       │   └── kustomization.yaml
│       └── staging
│           └── kustomization.yaml
├── m.txt
├── main.sh
├── monitoring
│   ├── cloudwatch
│   │   └── agent-config.json
│   ├── cloudwatch.sh
│   ├── cloudwatch.sh.bak
│   ├── grafana
│   │   ├── dashboards
│   │   │   └── kubernetes-cluster.json
│   │   └── datasources.yml
│   ├── logging.sh
│   ├── logging.sh.bak
│   └── prometheus
│       └── prometheus.yml
├── resource-patch.yaml
├── security
│   ├── certificates
│   │   └── certificate-config.yaml
│   ├── certificates.sh
│   ├── certificates.sh.bak
│   ├── policies
│   │   ├── iam-policies.json
│   │   ├── network-policies.yaml
│   │   └── rbac.yaml
│   ├── security.sh
│   └── security.sh.bak
├── set-env.sh
├── spec.txt
├── ss.txt
├── terraform-setup.sh
├── test-infrastructure.sh
├── update-deployments.sh
└── validate-manifests.old ```

73 directories, 163 files
costas778@LIT-CY-03:~/abc/trading-platform$ 



This project uses a number of different configuration files:

1. `.env` - Application runtime configuration
   - Contains non-sensitive application settings
   - Used by the application at runtime

2. `terraform.tfvars` - Infrastructure configuration
   - Contains non-sensitive infrastructure settings
   - Used by Terraform for infrastructure deployment

3. `set-env.sh` - Sensitive configuration
   - Contains AWS credentials and sensitive variables
   - Never committed to version control
   - Copy set-env.sh.template to set-env.sh and fill in your values

# Let's us begin!
**NOTE:** This whole environment was created in an AWS sandbox that is destroyed with the account number!

For the above files it is best to pass password and other sensitive parameters using AWS Key Manager.


Prerequisites
./install-prerequisites.sh 

Note: the above is for the Ubuntu distro using WSL2
Tip: I use a Codium extention that works as well as VSC. 

git clone https://github.com/costas778/Trading_platform_aws.git

1) Reconfigure AWS CLI:
First, check if there are any conflicting AWS environment variables:

I find this a useful exercise as I use various sandbox environments to create and update cpde which is on a timer.

env | grep AWS

Clear any existing AWS environment variables:

unset AWS_ACCESS_KEY_ID
unset AWS_SECRET_ACCESS_KEY
unset AWS_SESSION_TOKEN
unset AWS_SECURITY_TOKEN

Remove the existing credentials:

rm -rf ~/.aws/credentials
rm -rf ~/.aws/config

Now login into your AWS environments:

aws configure
Access Key: <place here> enter
Secret Key: <place here> enter
Region: east-us-1 enter
enter again

Confirm settings with aws configure list


2) Clear any old terraform files
tip: use multiple bash shells concurrently for this provisioning.

NOTE: If you have never provisioned this environment before there is no need to
do this! 

cd ~/abc/trading-platform/infrastructure/terraform/environments/dev$


# 1. Remove the .terraform directory (contains providers and modules)
rm -rf .terraform/

# 2. Remove all state files
rm -f terraform.tfstate
rm -f terraform.tfstate.backup

# 3. Remove the lock file
rm -f .terraform.lock.hcl

# 4. If you want to be thorough, remove crash log files too
rm -f crash.log


3) Obtain the hosted zone details

aws route53 create-hosted-zone --name abc-trading-dev.com --caller-reference $(date +%s)

{
    "Location": "https://route53.amazonaws.com/2013-04-01/hostedzone/Z097448413EYC12C8OCQ4",
    "HostedZone": {
        "Id": "/hostedzone/Z097448413EYC12C8OCQ4",
        "Name": "abc-trading-dev.com.",
        "CallerReference": "1739788255",
        "Config": {
            "PrivateZone": false
        },
        "ResourceRecordSetCount": 2
    },
    "ChangeInfo": {
        "Id": "/change/C00905481JW2DOJJAMZIQ",
        "Status": "PENDING",
        "SubmittedAt": "2025-02-17T10:30:55.677000+00:00"
    },
    "DelegationSet": {

   **The key is this "Id": "/hostedzone/Z097448413EYC12C8OCQ4"**

4) Obtain subnet details and place them within the infrastructure > terraform > environments > dev* > main.tf file


* or staging or Production depending on your deployment

aws ec2 describe-subnets --query 'Subnets[*].[SubnetId,VpcId,AvailabilityZone,CidrBlock]' --output table

For example:

module "eks" {
  source = "../../modules/eks"
  
  project_name       = var.project_name
  environment        = var.environment
  kubernetes_version = var.kubernetes_version
  subnet_ids        = [
    "subnet-0363c8537df9f3e34",  # us-east-1a
    "subnet-064d7dec2f261f295",   # us-east-1b
    "subnet-0b4cde3ea1aa2e226"   # us-east-1c

Note: I use 1a to 1c in this example


5) fill the placeholder information in various files with unique values

set-env.sh

#!/bin/bash
# AWS credentials
export AWS_ACCESS_KEY_ID="<your access key>"
export AWS_SECRET_ACCESS_KEY="<Your secret access key>"
export AWS_DEFAULT_REGION="us-east-1"

# Sensitive variables
export TF_VAR_database_password="<password>"

# Application runtime configuration
export BUILD_VERSION="1.0.0"
export DOMAIN_NAME="<xxx-xxxxxxxx-dev.com>"
export HOSTED_ZONE_ID="Zxxxxxxxxxxxxxxxxxx"

# Infrastructure configuration
export TF_STATE_BUCKET="bucketxxxxxxxxxxxx"
export TF_LOCK_TABLE="terraform-state-lock"
export CLUSTER_NAME="<xxx-xxxxxxxx-dev>"
export MICROSERVICES="axon-server backend-services frontend"

Note: I used the number of the account to create a unique bucket name.

.env

# Application runtime configuration
BUILD_VERSION=1.0.0
DOMAIN_NAME=abc-trading-dev.com
HOSTED_ZONE_ID="Zxxxxxxxxxxxxxxxxxxxxx"

# AWS Configuration
TF_STATE_BUCKET="bucketxxxxxxxxxxxxxxxxx"
TF_LOCK_TABLE="terraform-state-lock"
AWS_DEFAULT_REGION="us-east-1"

# Cluster Configuration
CLUSTER_NAME="<xxx-xxxxxxxxx-dev>"
MICROSERVICES="axon-server backend-services frontend"

# Database Configuration
DB_USERNAME="dbmaster"  # From terraform.tfvars
DB_PASSWORD="${TF_VAR_database_password}"  # From set-env.sh 
DB_PORT="5432"  # Standard PostgreSQL port
DB_INSTANCE_NAME="db_dev_xxxxxxxxxxxxxxxx"  # From terraform.tfvars

# Service Groups (new additions needed for microservices architecture)
CORE_SERVICES="authentication authorization user-management"
DEPENDENT_SERVICES="api-gateway audit cache compliance logging market-data message-queue"
BUSINESS_SERVICES="order-management portfolio-management position-management price-feed quote-service reporting risk-management settlement trade-execution notification"

# Legacy Configuration
MICROSERVICES="axon-server backend-services frontend"


infrastructure > terraform > environments > dev > terraform.tfvars

# Infrastructure configuration
aws_region          = "us-east-1"
environment         = "dev"
project_name        = "abc-trading"
cluster_name        = "abc-trading-dev"
vpc_cidr            = "10.0.0.0/16"
database_name       = "db_dev_xxxxxxxxxxxxxxx"
database_username   = "dbmaster"

availability_zones  = ["us-east-1a", "us-east-1b", "us-east-1c"]

# EKS configuration
kubernetes_version  = "1.28"
node_instance_type = "t3.medium"
node_desired_size  = 2
node_min_size      = 1
node_max_size      = 4

# RDS configuration
db_instance_class  = "db.t3.medium"
db_storage        = 20

Then once you have filled in the place holders run source set-env.sh to set the above values to
be global to the application.

6) Run test-infrastructure.sh to see that all the global variables are in place.

7) Ensure you have all the prerequisites installed by running install-prerequisites.sh if this is your first time
working on a system

8) Run the wrapper main.sh file with the deployment type.

e.g. ./main.sh dev

At the end run the following you will get the following:

Deploying database infrastructure...
Initializing the backend...
Initializing modules...
╷
│ Error: Backend configuration changed
│ 
│ A change in the backend configuration has been detected, which may require migrating existing state.
│ 
│ If you wish to attempt automatic migration of the state, use "terraform init -migrate-state".
│ If you wish to store the current configuration with no changes to the state, use "terraform init -reconfigure".

Go to a bash shell and run the following:
~/abc/trading-platform/infrastructure/terraform/environments/dev$ terraform init -reconfigure

Note: the directory will depend on the type of deployment. In this case its a dev

9) When you complete run the services after the initial provision to give you a total of 24 or so services

# Count total services
kubectl get services --all-namespaces | wc -l

You should, initally, get around 3

# Name of the services

kubectl get services --all-namespaces
or  kubectl get svc -A

better still
kubectl get svc -A -o custom-columns=NAMESPACE:.metadata.namespace,NAME:.metadata.name

An example of seeing services under a particular namespace
kubectl get svc -n kube-system

To get the full services deployed run the following:
./deploy-services.sh

# Run the same command account
kubectl get services --all-namespaces | wc -l

#then the following
kubectl get svc -A -o custom-columns=NAMESPACE:.metadata.namespace,NAME:.metadata.name

10) Run various tests

# Check specific services status
for service in $(kubectl get services -o name); do
    echo "Checking $service..."
    kubectl describe $service
done

# List all pods running in the kube-system
kubectl get pods -n kube-system

# List all running pods
kubectl get pods --all-namespaces

11) Dealing with the inital pod issue (certificates)


When you run kubectl get pods --all-namespaces you will see many pods NOT running! 

The installation requires post provisioning steps for the pods to load successful.

You first need to create and attach SSL certifcates to the pods for them to successfully load.

For SSL certificates, you have a few options:

a. Use AWS Certificate Manager (ACM) - Recommended approach for EKS:

# First create the certificate in ACM through AWS CLI
aws acm request-certificate \
    --domain-name "*.your-domain.com" \
    --validation-method DNS \
    --region us-east-1

# Then create a secret from the ACM certificate
aws acm get-certificate --certificate-arn <your-acm-certificate-arn> \
    --region us-east-1 | \
kubectl create secret tls trading-platform-certs \
    --cert=<(echo "$CERT") \
    --key=<(echo "$KEY")


b. Or, if you're using self-signed certificates for development:

NOTE: I used this option because my sandbox has limitations
and does not support the recommended method.  

Furthermore, this is instant rather than waiting between 5 and 30 minutes for the certificate to validate
using ACM! 

# Generate self-signed certificate
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout tls.key -out tls.crt \
    -subj "/CN=*.your-domain.com"

# Create the secret from generated certificates
kubectl create secret tls trading-platform-certs \
    --cert=tls.crt \
    --key=tls.key

12) Troubleshooting:

### The generic yet useful stuff

kubectl get events --sort-by='.lastTimestamp'
kubectl describe pod api-gateway-xxxxxxxxxxxxxxxxx
kubectl describe nodes

# Fix image pull policy in deployments
kubectl set image deployment/api-gateway api-gateway=your-registry/api-gateway:latest --record

# Update the node group to have larger instances
cd /home/costas778/abc/trading-platform/infrastructure/terraform/environments/dev
terraform apply -var='node_instance_type=t3.xlarge' -auto-approve

# Scale up the node group
aws eks update-nodegroup-config --cluster-name abc-trading-dev \
    --nodegroup-name abc-trading-dev-nodes \
    --scaling-config desiredSize=4,minSize=3,maxSize=6

connectivity
kubectl get events --sort-by='.lastTimestamp' | grep -i "network\|connection\|pull"

# Get node names
NODES=$(kubectl get nodes -o jsonpath='{.items[*].metadata.name}')

# Check connectivity from nodes
for node in $NODES; do
    echo "Checking connectivity for node: $node"
    kubectl debug node/$node -it --image=busybox -- ping -c 2 8.8.8.8
done

# Get VPC ID
VPC_ID=$(aws eks describe-cluster --name abc-trading-dev --query "cluster.resourcesVpcConfig.vpcId" --output text)

# Check NAT Gateway status
aws ec2 describe-nat-gateways --filter "Name=vpc-id,Values=$VPC_ID" --query 'NatGateways[*].[State,NatGatewayId]'

# Check route tables
aws ec2 describe-route-tables --filters "Name=vpc-id,Values=$VPC_ID" --query 'RouteTables[*].Routes'


# Get cluster security group
SG_ID=$(aws eks describe-cluster --name abc-trading-dev --query 'cluster.resourcesVpcConfig.clusterSecurityGroupId' --output text)

# Check security group rules
aws ec2 describe-security-groups --group-ids $SG_ID

Test API connectivity:

# Test frontend to backend communication
kubectl exec -it $(kubectl get pod -l app=frontend -o jsonpath='{.items[0].metadata.name}') -- curl -k https://backend-service

# Test backend to Axon server
kubectl exec -it $(kubectl get pod -l app=backend -o jsonpath='{.items[0].metadata.name}') -- curl -k http://axon-server:8024/actuator/health


Database connectivity test:

# Test backend to database connection
kubectl exec -it $(kubectl get pod -l app=backend -o jsonpath='{.items[0].metadata.name}') -- nc -zv database-service 5432


13) Horizontaly and Vertical scaling of the pods

# Horizontaly
First, check the current node capacity:

kubectl get nodes
kubectl describe nodes

Check all running pods to see what's consuming the capacity:

kubectl get pods --all-namespaces

You have a few options to resolve this:

a. Scale up the cluster (Recommended):

# Get current nodegroup name
aws eks list-nodegroups --cluster-name abc-trading-dev

# Scale up the nodegroup
aws eks update-nodegroup-config \
    --cluster-name abc-trading-dev \
    --nodegroup-name <your-nodegroup-name> \
    --scaling-config minSize=3,maxSize=6,desiredSize=3

b. Or clean up unnecessary pods:

# List all pods including their node allocation
kubectl get pods -o wide --all-namespaces

# Delete any unnecessary pods
kubectl delete pod <pod-name>

# vertically 

If you need help to increase CPU and memory resources for the pods you can use the following to apply the patch to all services using the following bash script:
./create-patches.sh

You may find that the pods are failing to schedule because the nodes are at capacity ("Too many pods"). This is a cluster resource limitation issue. In that case you
will need to scale down because your maxing out your resources you can apply the following patch to all services instead using this alternative bash script:
./update-deployments.sh

Both scripts are configuriable with your own metric needs! 



