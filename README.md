# Terraform Block


```
terraform {

  cloud {
    organization = "devshark"
    workspaces {
      name = "Example-Workspace"
    }
  }

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.27"
    }
  }

  required_version = ">= 0.14.9"
}
```


- terraform {} block contains Terraform settings
	- including the required providers Terraform will use to provision your infrastructure.
- For each provider, the source  attribute defines an optional hostname, a namespace, and the provider type
- Terraform installs providers from the Terraform Registry by default
- hashicorp/aws  => registry.terraform.io/hashicorp/aws
- required_providers set a version constraint for each provider
- version attribute is optional, but we recommend using it to constrain the provider version so that Terraform does not install a version of the provider that does not work with your configuration
	- If you do not specify a provider version, Terraform will automatically download the most recent version during initialization
	- https://www.terraform.io/language/providers/requirements


# Providers Block

```
provider "aws" {
  profile = "terraform"
  region  = "us-west-2"
}
```


- provider block configures the specified provider, such as aws . A provider is a plugin that Terraform uses to create and manage your resources.
- profile attribute in the aws provider block refers Terraform to the AWS credentials stored in your AWS configuration file
- You can use multiple provider blocks in your Terraform configuration to manage resources from different providers
- You can even use different providers together. For example, you could pass the IP address of your AWS EC2 instance to a monitoring resource from DataDog.

# Resources


```
resource "aws_instance" "app_server" {
  ami           = "ami-830c94e3"
  instance_type = "t2.micro"

  tags = {
    Name = "ExampleAppServerInstance"
  }
}
```

- resource blocks to define components of your infrastructure.
- A resource might be a physical or virtual component such as an EC2 instance, or it can be a logical resource such as a Heroku application.
- Resource blocks have two strings before the block
	- resource type
	- resource name
	- For example: 
		- resource type is `aws_instance`
		- name is `app_server`
- The prefix  of the type maps to the name of the provider.
	- In the example configuration, Terraform manages the aws_instance resource with the aws provider.
- Together, the resource type and resource name form a unique ID for the resource
	- For example, the ID for your EC2 instance is `aws_instance.app_server`
- Resource blocks contain arguments which you use to configure the resource. Arguments can include things like machine sizes, disk image names, or VPC IDs.


# Initialize the directory

Initializing a configuration directory downloads and installs the providers defined in the configuration, which in this case is the aws provider.


```
terraform init
```


## Format and validate configuration

```
terraform fmt
terraform validate
```


## Create/Apply infrastructure

```
terraform apply
```

- The output has a + next to aws_instance.app_server, meaning that Terraform will create this resource
- When the value displayed is (known after apply), it means that the value will not be known until the resource is created

##Inspect state

When you applied your configuration, Terraform wrote data into a file called terraform.tfstate

```
terraform show
```

- Tip: If your configuration fails to apply, you may have customized your region or removed your default VPC. Refer to the troubleshooting section at the bottom of this tutorial for help.

## Manually Managing state

```
terraform state list
```


# Change Infrastructure
# Destroy Infrastructure

```
terraform destroy
```


# Define Input Variables

- Create new file variables.tf
- Update main.tf with variables
- Apply changed configuration

Now apply the configuration again, this time overriding the default instance name by passing in a variable using the `-var` flag. Terraform will update the instance's `Name` tag with the new name. Respond to the confirmation prompt with `yes`

```
terraform apply -var "instance_name=YetAnotherName"
```


# Query data with output

- Create new file outputs.tf
- Add the configuration below to outputs.tf
```
output "instance_id" {
  description = "ID of the EC2 instance"
  value       = aws_instance.app_server.id
}

output "instance_public_ip" {
  description = "Public IP address of the EC2 instance"
  value       = aws_instance.app_server.public_ip
}

```
- Apply configuration
- Query the outputs with the `terraform out` command

# Store remote state
- In production environments you should keep your state secure and encrypted, where your teammates can access it to collaborate on infrastructure
- The best way to do this is by running Terraform in a remote environment with shared access to state.
- Terraform Cloud allows teams to easily version, audit, and collaborate on infrastructure changes. It also securely stores variables, including API tokens and access keys, and provides a safe, stable environment for long-running Terraform processes.

## Set up Terraform Cloud
- Create new account https://app.terraform.io/signup
- Create new Organization and workspace 
- Next, modify `main.tf` to add a `cloud` block to your Terraform Configuration 
```
  cloud {
    organization = "devshark"
    workspaces {
      name = "Example-Workspace"
    }
  }

```

Note: Because the cloud block is not supported by older versions of Terraform, you must use 1.1.0 or higher in order to follow this tutorial. Previous versions can use the remote backend block to configure the CLI workflow and migrate state.

## Login to Terraform Cloud
- Create new Token https://app.terraform.io/app/settings/tokens
- Login to Terraform Cloud with `terraform login`


## Initialize Terraform

```
terraform init
```

- Now that Terraform has migrated the state file to Terraform Cloud, delete the local state file.

```
rm terraform.tfstate
```

## Set workspace variables


![z3377156513282_468c7314829bd06d96de165c9365a6e4](https://user-images.githubusercontent.com/10163092/165888515-f2d98e1a-0811-4e5a-a376-2b5db78182c7.jpg)



- Apply configutation with `terraform apply` command
- Destroy your configuration

# Terraform language


- Blocks are containers for other content and usually represent the configuration of some kind of object, like a resource. Blocks have a block type, can have zero or more labels, and have a body that contains any number of arguments and nested blocks. Most of Terraform's features are controlled by top-level blocks in a configuration file.
- Arguments assign a value to a name. They appear within blocks.
- Expressions represent a value, either literally or by referencing and combining other values. They appear as values for arguments, or within other expressions.

Providers


Provider registry https://registry.terraform.io/browse/providers
