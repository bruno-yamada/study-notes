# Terraform Exam

- Terraform is based on the Declarative model through HCL (Hashicorp Configuration Language)

## Install and test PSI browser:
- https://syscheck.bridge.psiexams.com/?filters=system:bridge,version:standalone,code:ucs
- test again 24h before test
- can launch the exam within 30min of the appointment


# Docs
- handbook
  - https://hashicorp-certifications.zendesk.com/hc/en-us/articles/360048211571
- https://learn.hashicorp.com/collections/terraform/certification

# Scheduled for February 1st at 9am

---
> ref: https://learn.hashicorp.com/tutorials/terraform/associate-review?in=terraform/certification

# 1	Understand infrastructure as code (IaC) concepts
## 1a	Explain what IaC is
- process os managing infrastructure in a file or files rather than manually through a user interface
## 1b	Describe advantages of IaC patterns
- high level syntax, can be versioned, audited
  - more reliable (indempotency, consistency, repeatable, predictable)
    - scales
  - more manageable
    - can be organized into files/folders
    - can be reviewed
  - makes sense, mitigates risks
- can be shared and reused

# 2	Understand Terraform's purpose (vs other IaC)
## 2a	Explain multi-cloud and provider-agnostic benefits
- fault tolerance is limited to your provider, multicloud increases fault tolerancy
- terraform is cloud-agnostic, single configs can be used to manage multiple providers
## 2b	Explain the benefits of state
- maps config to real world
  - it expects it remote obj to be mapped to only one resource in state (otherwise things get messy)
- tracks metadata
  - resource dependencies, as trying to derive it automatically for every cloud explodes in complexity
  - also stores pointer to the provider used, in case multiple are present
- performance
  - stores attribute values for all resources in state
    - most optional, as it only gives performance gains
  - on all plan and applys, state is synced
  - having state to use as cache, along with `-refresh=false` and `-target` can save a lot of time on big infrastructures

# 3	Understand Terraform basics
## 3a	Handle Terraform and provider installation and versioning
- terraform
  - installation
    - pre-compiled binary, or compiled from source with `go install`
    - terraform -install-autocomplete
  - block
    - its block contains info of provider's version, source, provider_metadata
- provider block
  - name based on `required_providers` in the terraform block
  - can use input variables, but not attributes from resources (the resource depends on the provider), unless the attribute was configured and not generated after the apply
  - translates API interactions
  - has configurations of said provider
  - they can fetch values from ENV vars, to keep them away from versioned code
  - meta arguments
    - alias: for using the same provider, with different configurations, for different resources
    - version: not recommended, best configured at the terraform block `provider_requirements`
## 3b	Describe plugin based architecture
- Terraform uses a plugin-based architecture to support hundreds of infrastructure and service providers.
- `terraform init` downloads and installs providers based on provided configuration
- creates a lock file so that the same providers versions are used next time
## 3c	Demonstrate using multiple providers
```
provider "google" {
  project = "acme-app"
  region  = "us-central1"
}

# The default provider configuration; resources that begin with `aws_` will use
# it as the default, and it can be referenced as `aws`.
provider "aws" {
  region = "us-east-1"
}

# Additional provider configuration for west coast region; resources can
# reference this as `aws.west`.
provider "aws" {
  alias  = "west"
  region = "us-west-2"
}

```
use said provider using the `meta argumetn` __provider__ in each resource
```
resource "aws_instance" "foo" {
  provider = aws.west

  # ...
}
```
## 3d	Describe how Terraform finds and fetches providers
- through `provider` blocks, which might contain `version`(not recommended) or the version can be in the `required_providers` info in the terraform block
## 3e	Explain when to use and not use provisioners and when to use local-exec or remote-exec
```
resource "aws_instance" "web" {
  # ...

  provisioner "local-exec" {
    when    = destroy
    command = "echo The server's IP address is ${self.private_ip}"
    on_failure = continue|fail
  }
  provisioner "local-exec" {
    command = "echo 'created'"
  }
  provisioner "local-exec" {
    command = "echo 'another messagem saying created'"
  }
}
```
- can be used for: bootstrap resources, cleanup before destroy, config management, etc
- use as last resort
  - check if there is no provider support for that function
  - usually we have better options such as cloud-init scripts or user_data(aws)
- use when
  - flow cannot be declared used hcl
- disadvantages
  - cannot be properly tracked as anything can be done by them
- builtin: `file`, `local-exec`, `remote-exec`
- vendor: chef, habitat, puppet, salt-masterless
- new provisioners can be added by copying to folder `~/.terraform.d/plugins`
- provisioners run by default on creation, if they fail, resources are tainted (marked for destruction on next apply)
- if set to `when = destroy` they will run before destroying the resource, if fails, they wont destroy
- can have multiple provisioners blocks
  
# 4	Use the Terraform CLI (outside of core workflow)
## 4a	Given a scenario: choose when to use terraform fmt to format code
- applies terraform language style conventions, along some minor ajustments for readability
- ensures consistency, is opinionated, has no customization
```
terraform fmt
terraform fmt my-folder
cat file.tf | terraform fmt -
cat file.tf | terraform fmt -
terraform -list=false # dont list files changed
terraform -write=false # dont write changes
terraform -diff # shows diff?
terraform -check # returns 0 if everything is properly formatted
terraform -recursive # scans subdirectroies, by default scans only current dir
```
## 4b	Given a scenario: choose when to use terraform taint to taint Terraform resources
- marks a resource to be destroyed and __recreated__ on next apply
- does not modify infra, only state (to mark the resource)
- useful for recreating resources and everything associated with it (reconfiguring, reseting a resource, etc)
```
terraform taint aws_instance.my_instance
terraform taint "aws_instance.my_instance[0]"
terraform taint 'aws_instance.my_instance["one_instance"]'
```
flags:
```
-allow-missing # wont return error if the provided resource names does not exists
-backup=path # path to backup file
-lock=true # locks state file if supported
-lock-timeout=10s # duration to retry a state lock
-state=path # custom state path (defaults to terraform.tfstate)
-state-out=path # path to write the new state file (defaults to -state)
-ignore-remote-version # When using the enhanced remote backend with Terraform Cloud, continue even if remote and local Terraform versions differ. This may result in an unusable Terraform Cloud workspace, and should be used with extreme caution
```
## 4c	Given a scenario: choose when to use terraform import to import existing infrastructure into your Terraform state

## 4d	Given a scenario: choose when to use terraform workspace to create workspaces
## 4e	Given a scenario: choose when to use terraform state to view Terraform state
## 4f	Given a scenario: choose when to enable verbose logging and what the outcome/value is

5	Interact with Terraform modules
5a	Contrast module source options
5b	Interact with module inputs and outputs
5c	Describe variable scope within modules/child modules
5d	Discover modules from the public Terraform Module Registry
5e	Defining module version

6	Navigate Terraform workflow
6a	Describe Terraform workflow ( Write -> Plan -> Create )
6b	Initialize a Terraform working directory (terraform init)
6c	Validate a Terraform configuration (terraform validate)
6d	Generate and review an execution plan for Terraform (terraform plan)
6e	Execute changes to infrastructure with Terraform (terraform apply)
6f	Destroy Terraform managed infrastructure (terraform destroy)

7	Implement and maintain state
7a	Describe default local backend
7b	Outline state locking
7c	Handle backend authentication methods
7d	Describe remote state storage mechanisms and supported standard backends
7e	Describe effect of Terraform refresh on state
7f	Describe backend block in configuration and best practices for partial configurations
7g	Understand secret management in state files

8	Read, generate, and modify configuration
8a	Demonstrate use of variables and outputs
8b	Describe secure secret injection best practice
8c	Understand the use of collection and structural types
8d	Create and differentiate resource and data configuration
8e	Use resource addressing and resource parameters to connect resources together
8f	Use Terraform built-in functions to write configuration
8g	Configure resource using a dynamic block
8h	Describe built-in dependency management (order of execution based)

9	Understand Terraform Cloud and Enterprise capabilities
9a	Describe the benefits of Sentinel, registry, and workspaces
9b	Differentiate OSS and TFE workspaces
9c	Summarize features of Terraform Cloud
