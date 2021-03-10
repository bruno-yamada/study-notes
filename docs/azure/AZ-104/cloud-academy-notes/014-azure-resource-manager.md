# Azure Resource Manager (ARM)

It is the API used for managing resources on Azure

- Can be used through Azure CLI, Portal, PowerShell, SDKs
- can be used as IAC tool, seems to be the same as terraform or CloudFormation(aws)
- Uses JSON for templating
- templates are:
  - idempotent
  - repeatable

You can:
- download the template from a resource created through the portal using the `export template` function
- go to the resource group, select the resources, and click `export template`
- save the template to library
- use a template from the marketplace

Template contains:
- parameters
  - name, type, default values
- variables
  - reusable constant values
- resources
  - actual resource definition
  - everyone resource, has a name and type and can contain meta parameters (dependsOn)
- functions
  - like spreadsheet formular you can use in your template (array, max, if, contains, empty, length, trim)
  - can also create user-defined functions
- outputs
  - for displaying information of the created resources
  - useful for chaining templates

There is an VSCOde extension for ARM templates linting and intellisense

Templates executed from CLI are deployed to the cloud which executes the template

A template can make reference to `Linked Templates`, they must be stored online, and the outputs can be used in other templates

Deployment has 2 types:
- Incremental (default)
- Complete
  - removes resources not listed in the template resource group
  - recreates all resources, destroying existing ones
Deployments are binary, it either completes entirely with success, or not
