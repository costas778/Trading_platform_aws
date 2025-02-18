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

This project uses three types of configuration files:

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

Before running any commands:
```bash
source set-env.sh

# Your setup will be similar to the following:

tree
.
├── Bash.txt
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
└── validate-manifests.old

73 directories, 163 files
costas778@LIT-CY-03:~/abc/trading-platform$ 


