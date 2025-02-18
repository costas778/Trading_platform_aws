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

