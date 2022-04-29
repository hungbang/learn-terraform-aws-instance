#Quick start
## Download and install docker

```
open -a Docker
```

## Setup new learn terrafrom docker container

```
mkdir learn-terraform-docker-container
cd learn-terraform-docker-container
```

## Create new main.tf with below configuration

```
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 2.13.0"
    }
  }
}

provider "docker" {}

resource "docker_image" "nginx" {
  name         = "nginx:latest"
  keep_locally = false
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.latest
  name  = "tutorial"
  ports {
    internal = 80
    external = 8000
  }
}

```

## Initialize the Directory

```
terraform init
```

##Apply configuration

```
terraform apply
```

##Verify deployment

```
docker ps
```

##Destry infrastructure

```
terraform destroy
```