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
- imports a given resource by ID to a give resource address in the state
- flags:
```
-backup
-config=path # provider config to use for the import
-input
-lock
-lock-timeout
-no-color
-parallelism=n # limits number of concurrent operations as terraform walks the graph, defaults to 10
-state
-state-out
-var 'foo=bar'
-var-file=file
-ignore-remote-version # for TFE, ignores remote even if it differs
```
example:
```
terraform import 'aws_instance.baz["example"]' i-abcd1234
```

## 4d	Given a scenario: choose when to use terraform workspace to create workspaces
- some backends supports multiple `workspaces`
- so multiple instances of said configuration can be deployed without changing the backend config
- backends supporting workspaces: s3, AzureRM, Consul, kubernetes, local, posgres, remote
```
terraform workspace list
terraform workspace show # show current
terraform workspace new batata
terraform workspace select batata
terraform workspace delete batata
```
- cannot delete the default workspace
- can be use to interpolate configs:
```
resource "aws_instance" "example" {
  count = "${terraform.workspace == "default" ? 5 : 1}"

  # ... other arguments
}
```
- useful for creating parallel deployment with same config (A/B) before applying to prod
- useful for `feature branches`, `1 feature branch` = `1 workspace besides default`
- not suited for large projects
- workspace are technically equivalent to `renaming` the `state` file, nothing more complex than that
- current workspace stored in `.terraform` folder
- not applicable to terraform cloud
- if local, a folder `terraform.tfstate.d` is created to store workspaces

## 4e	Given a scenario: choose when to use terraform state to view Terraform state
```
terraform state list # lists alphabetically, then hierarchically
terraform state list 'module.aws_rds' # lists resources from that module only
# or by id
$ terraform state list -id=sg-1234abcd
module.elb.aws_security_group.sg
```

## 4f	Given a scenario: choose when to enable verbose logging and what the outcome/value is
- configured through setting `TF_LOG` to `TRACE`, `DEBUG`, `INFO`, `WARN` or `ERROR`
- logs defaults to stdout unless you set `TF_LOG_PATH`
- crash information is dunped to `crash.log`, reason can be find in the `panic:` line:
```
panic: runtime error: invalid memory address or nil pointer dereference

goroutine 123 [running]:
panic(0xabc100, 0xd93000a0a0)
    /opt/go/src/runtime/panic.go:464 +0x3e6
github.com/hashicorp/terraform/builtin/providers/aws.resourceAwsSomeResourceCreate(...)
    /opt/gopath/src/github.com/hashicorp/terraform/builtin/providers/aws/resource_aws_some_resource.go:123 +0x123
github.com/hashicorp/terraform/helper/schema.(*Resource).Refresh(...)
    /opt/gopath/src/github.com/hashicorp/terraform/helper/schema/resource.go:209 +0x123
github.com/hashicorp/terraform/helper/schema.(*Provider).Refresh(...)
    /opt/gopath/src/github.com/hashicorp/terraform/helper/schema/provider.go:187 +0x123
github.com/hashicorp/terraform/rpc.(*ResourceProviderServer).Refresh(...)
    /opt/gopath/src/github.com/hashicorp/terraform/rpc/resource_provider.go:345 +0x6a
reflect.Value.call(...)
    /opt/go/src/reflect/value.go:435 +0x120d
reflect.Value.Call(...)
    /opt/go/src/reflect/value.go:303 +0xb1
net/rpc.(*service).call(...)
    /opt/go/src/net/rpc/server.go:383 +0x1c2
created by net/rpc.(*Server).ServeCodec
    /opt/go/src/net/rpc/server.go:477 +0x49d
```


# 5	Interact with Terraform modules
- terraform recommends to use subdirectories for environments (thats probably why TFE supports `working directory`)
```
dev/
stg/
prod/
README.md
```
- helps:
  - understanding and navigating of complex configs
  - isolate updates and reduce risk of one update in one file having unintended effects
  - reduce duplication of code, which is very common if not using modules
  - facilitates sharing parts of configs between projects
  - ensure best practices (by using a module that implement best practice such as kms encryption or automatic backups for db instances) - can be local or remote

module block:
```
module "consul" {
  source  = "hashicorp/consul/aws"
  version = "0.0.5"
  servers = 3 # module input var

  # meta args
  count
  for_each
  providers
  depends_on
  licecycle
}
```
## 5a	Contrast module source options
- modules can be found in the official registry at registry.terraform.io, only verified modules appear in search results (assure estability)
- its integrated directly in terraform, so modules can be referred by `hashicorp/consul/aws` and it will be found in the registry
- privated modules
  - their source needs to be specified
```
# some might need credentials
module "vpc" {
  source = "app.terraform.io/example_corp/vpc/aws"
  version = "0.9.3"
}
```
- after 0.11 terraform will follow semant versioning to solve module version constraints
- publishing
  - done through the UI in terraform registry website, needs to authenticate to github and allow it register a webook
  - anyone can publish and shared modules
  - they are managed through git and github (required)
  - name must start with terraform-*
  - repo must use semantic tags (`v1.0.1`), which are used for firing the webhook also
  - repo description is copied to module
  - must follow the standard module structure
    - there must be a tf file in the root dir (main.tf, variables.tf, outputs.tf, etc, but it can be a single file)
    - root module and nested ones must have a README.md
    - recommended
      - nested modules under `modules/` folder with their own README.md
      - variables and outputs must have descriptions
## 5b	Interact with module inputs and outputs
- when using for the first time, must call `terraform init` or `terraform get`
  - init is based on required modules by source
  - get installs by provided name
  - they are installed in `.terraform/modules`

## 5c	Describe variable scope within modules/child modules
- inputs are like function arguments
- outputs are like return values
- local values are like a function's temporary values
```
variable "image_id" {
  type        = string
  description = "The id of the machine image (AMI) to use for the server."
  default = "ami-askdfalkdfj"
  sensitive = false # doesnt show value in plan/apply
  validation {
    condition     = length(var.availability_zone_names) > 4 && substr(var.image_id, 0, 4) == "ami-"
    error_message = "The image_id value must be a valid AMI id, starting with \"ami-\"."
  }
}
```
- setting values
  - through TFE workspace variables
  - with `-var='foo=bar'`
  - with `-var-file=file.tfvars`
  - automatically in files: `terraform.tfvars`, `terraform.tfvars.json`
  - files ending with `auto.tfvars` or `auto.tfvars.json`
  - as environment variables: TF_VAR_foo=bar (terraform may attempt to parse the value based on type contraints (map, tuple, list, etc))
- loading order
  - Env vars
  - terraform.tfvars
  - terraform.tfvars.json
  - *.auto.tfvars or *.auto.tfvars.json
  - `-var` or `-var-file` arguments
- outputs
  - can be used by child modules to pass values onto parents
  - can be used by root modules to print as CLI output
  - can be accessed through `terraform_remote_state` as data block
```
output "instance_ip_addr" {
  depends_on = module.rds.main
  description = ""
  sensitive = false
  value = aws_instance.server.private_ip
}
```
- a module can only access variables passed to it, and it can pass variables to parent through outputs

## 5d	Discover modules from the public Terraform Module Registry
- refer to 5a

## 5e	Defining module version
- refer to 5

# 6	Navigate Terraform workflow
- write, plan, apply(create)

## 6a	Describe Terraform workflow ( Write -> Plan -> Create )
- write
  - to avoid burden and security risks, its better to delegate plan/apply to a CI/CD pipeline
  - versioned code and git workflow allows revision and discussions with other devs
  - while writing, continue doing plans to write out syntax errors, missing variables, etc
- plan (syncs state)
  - once you done writing it is time do submit for the final plan
  - produces speculative plan output for the team to review the changes to infrastructure in full
  - also allows to check wether the change should be made now or during an maintenance window
- apply (syncs state)
  - after merges, check the actual changes to infrastructue, it might have changed since the last plan
  - check if this change needs an maintenance window, maybe it causes service disruption
  - actually makes the changes to infrastructure

## 6b	Initialize a Terraform working directory (terraform init)
- safe to run multiple times (will never delete config or state)
- modules are searched and installed based on the `version` and `source` value in their blocks
- providers are installed in the `./terraform/plugins` dir

```
-input=true # ask input if necessary
-lock=false # disables locks if lock is support and state is necessary during init
-lock-timeout=10s
-no-color
-upgrade # upgrades modules (based on module|providers version semver)
-get-plugins=false # avoid plugins installation
-plugin-dir=PATH # to set plugin installation dir, useful for caching
-from-module=SOURCE_MODULE # if you want to init from another module into your current folder
-get=false # avoid child module installation
```
- backend config is consulted on init
```
-force-copy # answers yes when asked if should copy state (e.g. when there is local state being migrated to remote state)
-backend-config=file # for when backend config is generated dynamically
```

## 6c	Validate a Terraform configuration (terraform validate)
- check if config is syntethically right and internally consistent
- can be used without accessing the backend with `terraform init -backend=false`
```
-json # json output
-no-color 
```

## 6d	Generate and review an execution plan for Terraform (terraform plan)
- creates the execution plan
- performs a sync and derives the actions needed to reach the desired state
- can use `-out` to save the plan and execute it later with `terraform apply PLAN_FILE`
```
-compact-warnings # include only summary messages
-destroy # generates plan to destroy the resources
-detailed-exitcode # changes from always returning returning 0 to: 0=no-changes, 1=error, 2=has_changes
-input=true|false # wether to ask for missing inputs
-lock=true
-lock-timeout
-no-color
-out=PATH
-parallelism=N # how many concurrent operations, defaults to 10
-refresh=true # wether to update the state before generating the plan
-state=path # defaults to `terraform.tfstate`, ignores if using remote state
-target=resource # performs the plan only for this resource, can be present multiple times, if it is a module, will include sub-resources
-var `foo=bar`
-var-file=file
```
- execution plans from `-out` may contain secrets

## 6e	Execute changes to infrastructure with Terraform (terraform apply)
- applies the changes required to reach the desired state, or the actions of a `execution plan`
```
-backup=path # path to save a bkp of the state file
-compact-warnings
-lock=true
-lock-timeout=0s
-input=true # ask for input for variables not directly set
-auto-approve
-no-color
-parallelism=n
-refresh=true # wether to update the state before applying, skipped if using a plan
-state=path
-state-out=path
-var 'foo=bar'
-var-file=foo
```

## 6f	Destroy Terraform managed infrastructure (terraform destroy)
- destroys the terraform-managed infrastructure
- asks for confirmation
- accepts all arguments from the apply command, except for the execution plan
- if `-target` is set, will destroy that resource and the resources that `depends_on` it
- you can also preview it with `terraform plan -destroy`
- terraform destroys them in a suitable order, based on the dependancy graph

# 7	Implement and maintain state
## 7a	Describe default local backend
- backend is were state snapshots are stored
- `local` recommended only for learning, otherwise use remote backend
- the CLI uses the backend config block, TFE uses its own stored backend
  - if you use TFE, you can configure the backend to the proper remote TFE state, to be able to run `plan` locally before commiting
- you can only use the `builtin backends`
- state maps config to real world
- remote backend allows multiple people to access it, better since it promotes collaboration, also requires credentials
- be careful as state can store sensitive information
- some backends supports state locking
- state and operations:
    - state: local or remote
    - operations: executed against infrastructure APIs
      - terraform can perform operations locally or remotelly
    - state can be stored remotely and operations executed locally
- backend types:
  - enhanced: stores states but can also perform operations
  - standard: only stores state, `local` is used for exeuting operations
- __the only enhanced backends are 'local'(local fs) and 'remote'(TFE)__
- in the case of `remote` backend, in TFE you can opt to run operations locally
```
# local (is enhanced since operations are also executed locally)
terraform {
  backend "local" {
    path = "relative/path/to/terraform.tfstate"
  }
}

# remote S3 (is standard, since operations have to be executed locally with this backend)
# supports locking through DynamoDB
terraform {
  backend "s3" {
    bucket = "mybucket"
    key    = "path/to/my/key"
    region = "us-east-1"

    # by configuring the below options, you enable state locking
    dynamodb_endpoint = ""
    dynamodb_table = ""
  }
}

# remote TFE (TFE can also execute operations, thus it is a `enhanced` backend)
terraform {
  backend "remote" {
    hostname = "app.terraform.io"
    organization = "company"

    workspaces {
      name = "my-app-prod"
    }
  }
}
```

## 7b	Outline state locking
- if supported, TF will lock state, preventing concurrent write operations (useful for working in a team, which could generate corrupted state)
- there is a `force-unlock` option, useful when automatic unlocking failed for some reason, when unlocking fails, TF outputs an ID which is used for the force-unlocking

## 7c	Handle backend authentication methods
- each standard backend has its own way for authenticating, it is usually composed of an user/pass combo or an authentication token

## 7d	Describe remote state storage mechanisms and supported standard backends
supported remote standard backends:
### 7d.1 Without Locking
- artifactory
  - url, user pass, url repo, subpath
- etcd (etcd v2)
### 7d.1 With Locking
- azurerm (uses blob storage)
```
terraform {
  backend "azurerm" {
    storage_account_name = "abcd1234"
    container_name       = "tfstate"
    key                  = "prod.terraform.tfstate"

    # rather than defining this inline, the SAS Token can also be sourced
    # from an Environment Variable - more information is available below.
    sas_token = "abcdefghijklmnopqrstuvwxyz0123456789..."
  }
}
```
- consul (Consul KV Storage)
```
terraform {
  backend "consul" {
    address = "consul.example.com"
    scheme  = "https"
    path    = "full/path"
  }
}
```
- cos (Tencent Cloud Object Storage)
- etcdv3 (etcd KV store)
- gcs (Google Cloud Storage)
- http
  - stores remotely, using standard REST Api verbs, with optional locking based on what the API supports
```
terraform {
  backend "http" {
    address = "http://myrest.api.com/foo"
    lock_address = "http://myrest.api.com/foo"
    unlock_address = "http://myrest.api.com/foo"
  }
}
```
- kubernetes
Stores in k8s Secrets
```
terraform {
  backend "kubernetes" {
    secret_suffix    = "state"
    load_config_file = true
    labels =
    namespace = 
    host =
    username = 
    password =
    ...
  }
}
```
- manta (joyent distributed Object Storage, lock withing manta)
- oss (Alibaba cloud OSS, locking via TableStore)
- pg (postgres)
- s3 (aws s3, lock with DynamoDB)
- swift (OpenStack distributed Blob Storage)

## 7e	Describe effect of Terraform refresh on state
- reconciles state with real-world infrastructure
- helps detect configuration drift
- does not modify infrastrucutre, but does modify state
  - updated state might cause changes to occur on next apply
flags:
```
-backup=path
-compact-warnings
-input=true
-lock=true
-lock-timeout=10s
-no-color
-parallelism=n
-state=path
-state-out=path
-target=resource
-var 'foo=bar'
-var-file=foo
```

## 7f	Describe backend block in configuration and best practices for partial configurations
```
terraform {
  backend "remote" {
    organization = "<ORG_NAME>"
    workspaces {
      name = "Example-Workspace"
    }
  }
}
```

## 7g	Understand secret management in state files 
- states contains IDs for all resources, for databases it main contain the initial password
- local state is kept in plain json
- remote is only held in memory during utilization
- remote can be encrypted at rest depending on the remote backend
- if sensitive information is managed(datbase password, user password, private keys), state should be considered sensitive as well
- TFE encrypts at rest, and uses TLS in transit, also keeps history and allow authorization and audit logging
- S3 supports encryption at rest, IAM policies can be used for authorization, TLS for in transit and logs can be kept at CloudWatch

# 8	Read, generate, and modify configuration
## 8a	Demonstrate use of variables and outputs
- variable definition and usage:
```
variable "region" {
  default = "us-west-2"
}
provider "aws" {
  region = var.region
}

$ terraform plan -var 'region=us-east-1'
or
$ terraform plan -var-file=env.tfvars
$ terraform plan # with variables in terraform.tfvars or env.auto.tfvars

```
- outputs
```
output "ami" {
  value = aws_instance.example.ami
}
---
$ terraform apply -var region=us-west-2

Apply complete! Resources: 0 added, 0 changed, 0 destroyed.

Outputs:

  ami = ami-def456
```

## 8b	Describe secure secret injection best practice
> further read: https://learn.hashicorp.com/tutorials/terraform/secrets-vault

- it is recommended to avoid putting secrets in the tf state file
- using vault
  - sensitive credentials are stored in vault, so they stay out of the state
  - only people/services with proper credentials can fill read credentials from vault to use the credentials in terraform
  - tf requests a __short lived__ token from vault to use in its operations(defaults to 20min)
    - the policy for the user needs to allow him to create those short-lived secrets
    - if you output an execution plan, it will have the above short lived token, if the apply takes longer than the TTL of the token, the apply will fail
Sample Vault usage:
```
provider "vault" {
  # env vars: VAULT_ADDR, VAULT_TOKEN, VAULT_TOKEN_NAME, VAULT_CACERT, VAULT_CAPATH, etc
  # It is strongly recommended to configure this provider through the
  # environment variables described above, so that each user can have
  # separate credentials set in the environment.
  #
  # This will default to using $VAULT_ADDR
  # But can be set explicitly
  # address = "https://vault.example.net:8200"
}

resource "vault_generic_secret" "example" {
  path = "secret/foo"

  data_json = <<EOT
{
  "foo":   "bar",
  "pizza": "cheese"
}
EOT
}
```

## 8c	Understand the use of collection and structural types
- primitive types
  - string
  - number
  - bool
- complex types groups multiple values into a single value
- collection type: multiple values of `one` type
  - list:
    - `list(string)` sequence of string values, identified by whoel numbers starting with zero
    - `list` equivalent to `list(any)`
  - map:
    - map(number): collection of numbers, identified by a string label (e.g. {'cluster_a_size': 1, 'cluster_b_size': 2})
    - map: shorthand for `map(any)`
  - set:
    - collection of __unique__ values, with not secondary identifier or order.
- structural type: allow multiple values of distincts types to be grouped together, requires schema
  - object
    - e.g. `object({name=string, age=number})`: value with a specified schema and type contraints, would match `{name="John", age=20}`
  - tuple
    - e.g. `type([string, number])`: thus it needs two values of specific types, would match `["John", 20]`
- TF tries to convert types according to the variable type definition
  - lists converted to tuples lose duplicated values
  - tuples converted to list gains a fixed order (based on the values, alphabetically, numerical, etc)
- TF 0.14 adds the experimental `optional` keyword for type definition
  - activate this feature with `experiments = [module_variable_toptional-_attrs]` in the `terraform` configuration block
  - `object(name=string, age=optional(number))`

## 8d	Create and differentiate resource and data configuration
### 8d.1 Resource
- describes one or more infrastructure object
- keyword `resource` followed by type:`aws_instance` and a local name:`web`
- the local name is only used within the module scope
- the curly braces start and end the config block with the arguments in between
```
resource "aws_instance" "web" {
  ami           = "ami-a1b2c3d4"
  instance_type = "t2.micro"
}
```
- meta arguments:
```
depends_on # for specifying hidden dependencies
count # for creating multiple resource instances according to a count
for_each # to create multiple instances according to a map, or set of strings
provider # for selecting a non-default provider configuration
lifecycle # for lifecycle customizations
provisioner and connection # for taking extra actions after resource creation
```
Some(rare) support the `timeouts` block:
```
resource "aws_db_instance" "example" {
  # ...

  timeouts {
    create = "60m"
    delete = "2h"
  }
}
```

### 8d.2 Data
- allows data to be fetched or computed, to be used elsewhere
- allows outside information to be used to provide your infrastructure configuration
- each `provider` can offer a number of data sources alongisde the `resource` types
- meta-arguments supported, all except lifecycle:
  - count, for_each, provider, depends_on, provisioner and connection
```
# reads from remote `aws_ami` resources, based on the filters and expose it with local name `example`
data "aws_ami" "example" {
  most_recent = true

  owners = ["self"]
  tags = {
    Name   = "app-server"
    Tested = "true"
  }
}

# usage
```
data.aws_ami.example.id
```
- some data resources are local only, useful for reading context from files or templates of files
- lifecycle:
  - if the data does not depends on infra not yet created # it will be fetched during the `refresh` phase, so the values are available during the `plan` phase
  - otherwise they appear as `computed` during the plan phase and be available after the resource is created

## 8e	Use resource addressing and resource parameters to connect resources together

### 8e.1 Module Spec
- is a string that references a specific resource 
```
[module path].[resource spec]
```
can have nested modules, e.g.
```
module.rds[0].module.mysql_rds.aws_instance.main
```
> modules indexes only after 0.13, before you need to create each module separately

### 8e.2 Resource Spec
- addresses a specific resource in the config
```
resource_type.resource_name[resource index]
```
give a for_each:
```
resource "aws_instance" "web" {
  # ...
  for_each = {
    "terraform": "value1",
    "resource":  "value2",
    "indexing":  "value3",
    "example":   "value4",
  }
}
```
the addresses would be:
```
aws_instance.web["terraform"]
aws_instance.web["resource"]
aws_instance.web["indexing"]
aws_instance.web["example"]
```

### 8e.3 Named values
- resources
  - `<resource_type>.<name>`
- input variables
  - `var.<name>`
- local values
  - `local.<name>`
- child modules outputs
  - `module.<module_name>...`
- data sources
  - `data.<data_type>.<name>`
- filesystem and workspace info
  - `path.module`: path of the module where the expression is placed
  - `path.root`: path of the root module
  - `path.cwd`: path of the working directory from were the configuration is being invoked from
  - `terraform.workspace`: current selected workspace
- block-local values
  - `count.index`: from the `count` meta argument
  - `each.key` and `each.value`: from the `for_each` meta argument
  - `self`: for usage in the `provisioner` and `connection` blocks

> usage of some values(like resource and modules) in other resources/modules creates an implicit dependency between them

sensitive attributes:
- resource types can have certain atributes marked as sensitive, thus they will be redacted from the plan output, (although values derived from them will not be redacted)
- 0.14 onwards you can enable a flag so that derived values will also be redacted, working the same as with `sensitive variables` currently.

## 8f	Use Terraform built-in functions to write configuration
- numeric
  - `abs`: returns the absolute value (negative becomes positive)
  - `ceil`
  - `floor`
  - `log`: return logarithm of number: `log(16,2) = 4`
  - `max`: `max(3, 4, 1)=4`
  - `min`: `min(3, 4, 1)=1`
  - `parseint`: parses the given base: `parseint("100", 10)=100`, `parseint("FF", 16)=255`
  - `pow`: `pow(3, 2)=9`
  - `signum`: returns the sign: `signum(0)=0`, `signum(3)=1`, `signum(-3)=-1`
- string
  - `chomp`: remotes new line from the end of line `chomp("hello\n")`
  - `format`: similar to printf from `C`, `format("Hello %s!", "John")`
  - `formatlist`: same as above, but with list: `formatlist("Hello%s!", [1, 2, 3]) = ["Hello1!", "Hello2!",  "Hello3!"]`
  - `indent`: indent the contents of a variable after the first line: `"  items: ${indent(2, "[\n  foo,\n  bar,\n]\n")}"`
  - `join`: `join(", ", ["foo", "bar", "baz"])`
  - `lower`: `lower("John-Doe")="john-doe"`
  - `regex`: 
    - if there is no capture group, return string with substring matched by the pattern
    - if has unnamed capture groups, return a list of substring matched
    - if has named capture groups, return a map of substring matched
    - if nothing matches, an error is thrown
    - e.g.
      - `regex("(\\d\\d\\d\\d)-(\\d\\d)-(\\d\\d)", "2019-02-01") = ["2019", "02", "01"]`
      - `regex("(?P<year>\\d{5})-(?P<month>\\d{2})-(?<day>\\d{2})", "2019-02-01") = {"year": 2019", "month": "02", "day": "01"]`
  - `regexall`: 
    - same as `regex`, except:
      - always returns a list
      - if there is no match, returns empty list
      - if there is unnamed captures, return list of lists
      - if there is named captures, return list of maps
        - `regex`: `regex("[a-z]+", "1234abcd5678efgh9") = "abcd"`
        - `regexall`: `regexall("[a-z]+", "1234abcd5678efgh9") = ["abcd", "efgh"]`
  - `replace`: 
    - `replace("1 + 2 + 3", "+", "-") = "1 - 2 - 3"`
    - also accepts regex: `replace("hello world", "/w.*d/", "everybody") = "hello everybody"`
  - `split`: `split(",", "foo,bar,baz") = ["foo", "bar", "baz"]`
  - `strrev`: reverses string `strrev("foo") = "oof"`
  - `substr`: `substr("hello world", 1, 4) = "ello"`
  - `title`: `title("hello world") = "Hello World"`
  - `trim`: removes specified chars from beginning and end of string `trim("!hello!", "!") = "hello"`
  - `trimprefix`: `trimprefix("!hello!", "!") = "hello!"`
  - `trimsuffix`: `trimsuffix("!hello!", "!") = "!hello"`
  - `trimspace`: `trimspace("  hello\n") = "hello"`
  - `upper`
- collection
  - `alltrue`: all values of a collection must resolve to a `bool` true
  - `anytrue`: any value of a collection must resolve to a `bool` true
  - `chunklist`: `chunklist(["a", "b", "c", "d", "e"], 2) = [["a", "b"],  ["c", "d"],  ["e"]]`
  - `coalesce`: returns first non-null or empty element `coalesce("", "b") = "b"`
  - `coalescelist`: returns first non-empty list `coalescelist([], ["c", "d"]) = ["c", "d"]`
  - `compact`: removes empty elements from a list
  - `concat`: combines lists into a single one
  - `contains`: `contains(["a", "b", "c"], "a") = true`
  - `distinct`: returns list without duplicates
  - `element`: get element from list by idx
  - `flatten`: takes multiple complex lists and returns a list of a single level with its elements
  - `index`: given a list and an element, find its index
  - `keys`: returns the keys of a map
  - `length`: length of a list, or of a map's keys
  - `list`: creates a list
  - `lookup`: get element by key or return default `lookup(map, key, default)`
  - `map`: creates a map `map("a", "b", "c", "d") = {"a": "b", "c": "d"}`
  - `matchkeys`: returns match based on `matchkeys(valueslist, keyslist, key_to_find_list)`
  - `merge`: merges maps
  - `range`: `range(max) or range(start, limit, step)`
  - `reverse`: reverse list
  - `setintersection`: return common values given multiple lists/sets
  - `setproduct`: gives the multiple of two sets: `setproduct(["a", "b"], ["1", "2"]) = [["a", "1"],   ["a", "2"],  ["b", "1"]...]`
  - `setsubtract`: gives the subtraction of 2 sets/list
  - `setunion`: similar to concat, but returns a set, thus has no duplicates
  - `slice`: `slice(list, startIDX, endIDX)`, `slice(["a", "b", "c", "d"], 1, 3) = ["b", "c"]`
  - `sort`: sort elements of a list
  - `sum`: sum values of a list
  - `transpose`: transposes the matrix
  - `values`: return values of a map
  - `zipmap`: given two lists, makes a map `zipmap(keyslist, valueslist)`
- encoding
  - `base64decode`:
  - `base64encode`:
  - `base64gzip`: compresses with gzip, then base64 encodes
  - `csvdecode`: from csv(header + rows) to list of maps
  - `jsondecode`:
  - `jsonencode`:
  - `textdecodebase64`: uses a specific encoding `textdecodebase64("SABlAGwAbABvACAAVwBvAHIAbABkAA==", "UTF-16LE")`
  - `textencodebase64`:
  - `urlencode`:
  - `yamldecode`:
  - `yamlencode`:
- filesystem
  - `abspath`: `abspath(path.root) = "/home/user/some-dir/terraform/my-module"`
  - `dirname`: `dirname("foo/bar/baz.txt") = foo/bar`
  - `pathexpand`: `pathexpand("~/.ssh/id_rsa") = /home/steve/.ssh/id_rsa`
  - `basename`: `basename("foo/bar/baz.txt") = baz.txt`
  - `file`: return contents of a file as string
  - `fileexists`:
  - `fileset`: `fileset(path.module, "files/*.txt") = [ "files/hello.txt", "files/world.txt" ]`
  - `filebase64`: return file contents base64 encoded
  - `templatefile`: returns a an template file filled by its config 
```
templatefile("${path.module}/config.tmpl", {
    config = {
      "x"   = "y"
      "foo" = "bar"
      "key" = "value"
    }
  }
)
```
- date and time
  - `formatdate`: converts a timestamp string into a different format `formatdate("HH:mmaa", "2018-01-02T23:12:01Z") = 11:12pm`
  - `timeadd`: ` timeadd("2017-11-22T00:00:00Z", "10m")  = 2017-11-22T00:10:00Z `
  - `timestamp`: returns current timestamp
- hash and crypto
  - `base64sha256`: computes the SHA256 hash of a given string and encodes it with Base64
  - `base64sha512`:
  - `bcrypt`:
  - `filebase64sha256`: same as `base64sha256` but with a file
  - `filebase64sha512`:
  - `filemd5`:
  - `filesha1`:
  - `filesha256`:
  - `filesha512`:
  - `md5`:
  - `rsadecrypt`: `rsadecrypt(ciphertext, privatekey)`
  - `sha1`:
  - `sha256`:
  - `sha512`:
  - `uuid`: `uuid() = b5ee72a3-54dd-c4b8-551c-4bdc0204cedb`
  - `uuidv5`: ` uuidv5("url", "https://www.terraform.io/") = 9db6f67c-dd95-5ea0-aa5b-e70e5c5f7cf5 `
- IP Network functions
  - `cidrhost`: given a cidr and a host num, return its IP `cidrhost("10.12.127.0/20", 16) = 10.12.112.16`
  - `cidrnetmask`: returns the mask of a CIDR block `cidrnetmask("172.16.0.0/12") = 255.240.0.0`
  - `cidrsubnets`: given a cidr, and the bits for the subnet, return the subnets `cidrsubnets("10.1.0.0/16", 4, 4) [ "10.1.0.0/20", "10.1.16.0/20"] `
  - `cidrsubnet`: given the cidr, the subnet bits, and an subnet num, return its cidr `cidrsubnet(prefix, newbits, netnum)`
- Type conversion
  - `can`: encapsulate an function or expression, returns false if the expression would throw an error
  - `defaults`: uses a value or the default if there is no value `instance_type = defaults(var.instance_type, 't2.medium')`
  - `tobool`: returns the bool value of a variable
  - `tolist`: ` tolist(["a", "b", 3]) = ["a", "b", "3"]`
  - `tomap`: 
  - `tonumber`: `tonumber("1") = 1`
  - `toset`: `toset(["c", "b", "b"]) = ["c", "b"]`
  - `tostring`: `tostring(1) = "1"`
  - `try`: returns first successful operation `try( [tostring(var.example)], tolist(var.example),)`

## 8g	Configure resource using a dynamic block
- dynamically creates attributes on a resource, requires the attribute name("setting"), for_each loop, and the content of the attribute
- avoid using too much as it can make config hard to read
```
resource "aws_elastic_beanstalk_environment" "tfenvtest" {
  name                = "tf-test-name"
  application         = "${aws_elastic_beanstalk_application.tftest.name}"
  solution_stack_name = "64bit Amazon Linux 2018.03 v2.11.4 running Go 1.12.6"

  dynamic "setting" {
    for_each = var.settings
    content {
      namespace = setting.value["namespace"]
      name = setting.value["name"]
      value = setting.value["value"]
    }
  }
}
```

## 8h	Describe built-in dependency management (order of execution based)
- affects the order of creation and destruction (reverse from creation)
__explicit__: through `depends_on` meta argument
```
resource "aws_s3_bucket" "example" {
  acl    = "private"
}

resource "aws_instance" "example_c" {
  ami           = data.aws_ami.amazon_linux.id
  instance_type = "t2.micro"

  depends_on = [aws_s3_bucket.example]
}
```

__implicit__: when one resource references the property of another resource
```
resource "aws_instance" "example_b" {
  ami           = data.aws_ami.amazon_linux.id
  instance_type = "t2.micro"
}

resource "aws_eip" "ip" {
    vpc = true
    instance = aws_instance.example_a.id
}
```

# 9	Understand Terraform Cloud and Enterprise capabilities
## 9a	Describe the benefits of Sentinel, registry, and workspaces
- Sentinel
  - IaC policies
  - runs after plan, but before apply
  - Sentinel CLI allows you to test it before deploy
```
import "time"

# Validate time is between 8 AM and 4 PM
valid_time = rule { time.now.hour >= 8 and time.now.hour < 16 }

# Validate day is M - Th
valid_day = rule {
    time.now.weekday_name in ["Monday", "Tuesday", "Wednesday", "Thursday"]
}

main = rule { valid_time and valid_day }
```
- TF cloud allows you to share modules within you organization
- Workspaces
  - is similar to isolating your code in folders
  - config is stored in VCS or upload through API
  - variables are stored in the workspace
  - state is saved on the workspace
  - credentials are stored in the workspace as `sensitive` values
  - 3 types:
    - VCS - triggers based on git repos
    - CLI - triggers from local CLI, (operations can still be executed remotely)
    - API - more advanced, useful to integrate into pipelines

## 9b	Differentiate OSS and TFE workspaces
- local workspaces are like using multiple state files
- TFE workspaces gather a lot: `state` # `variables`, `Env vars`, `sensitive credentials`
  - multiple workspaces locally have to be translated into multiple workspaces on TFE
    - so you would have: `my-infra-dev`, `my-infra-stg` and `my-infra-prod` workspaces in TFE

## 9c	Summarize features of Terraform Cloud
- Main Features
  - Remote operations
  - Remote state with locking
  - Remote variables
  - Governance
    - Authorization and Authentication
    - Team-Based permissions (paid tier)
    - Policy enforcing through Sentinel
    - Cost Estimation (for AWS, GCP and Azure)
  - VCS integration
  - CLI integration
  - private Module registry
  - API
  - Notifications (Slack, E-mail, Webhooks...)

# Extras
- [TF Environment Variables](https://www.terraform.io/docs/cli/config/environment-variables.html#tf_data_dir)
  - TF_INPUT=0 # useful for pipelines
  - TF_VAR_name # fill a variable using Environment variables
  - TF_CLI_ARGS
    - TF_CLI_ARGS="-input=false"
    - TF_CLI_ARGS_plan="-refresh=false"
  - TF_DATA_DIR # change location of `.terraform`, defaults to to current dir, useful if curr dir is not writable
  - TF_WORKSPACE # `export TF_WORKSPACE=prod` saves you from running `terraform workspace select prod`
  - TF_IN_AUTOMATION # easy variable to set if running inside automation, keeps terraform from asking from input during execution
  - TF_REGISTRY_DISCOVERY_RETRY
  - TF_REGISTRY_CLIENT_TIMEOUT
  - TF_CLI_CONFIG_FILE
- `.terraformignore`: ignores certain files from being upload to the cloud for plan and apply operations
- [TF cli](https://www.terraform.io/docs/cli/commands/index.html)
## lifecycle
```
resource "aws_instance" "example" {
  # ...

  create_before_destroy = true
  prevent_destroy = false

  lifecycle {
    ignore_changes = [
      # Ignore changes to tags, e.g. because a management agent
      # updates these based on some ruleset managed elsewhere.
      tags,
    ]
  }
}
```
## for_each
```
resource "aws_subnet" "example" {
  for_each = var.subnet_numbers

  vpc_id            = aws_vpc.example.id
  availability_zone = each.key
  cidr_block        = cidrsubnet(aws_vpc.example.cidr_block, 8, each.value)
}
```
## count
```
resource "aws_instance" "server" {
  count = 4 # create four similar EC2 instances

  ami           = "ami-a1b2c3d4"
  instance_type = "t2.micro"

  tags = {
    Name = "Server ${count.index}"
  }
}
```
