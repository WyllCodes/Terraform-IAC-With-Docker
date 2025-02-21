# Terraform-IAC-With-Docker

## Steps to create a docker container using Infrastructure as code with Terraform

Create a directory named learn-terraform-docker-container.
```
  mkdir learn-terraform-docker-container
```
Change into the Directory
```
cd learn-terraform-docker-container
```
Create a file to define your infrastructure
```
touch main.tf
```
Open main.tf in your text editor, paste in the configuration below, and save the file.

 ```
 terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 3.0.1"
    }
  }
}

provider "docker" {
  host    = "npipe:////.//pipe//docker_engine"
}

resource "docker_image" "nginx" {
  name         = "nginx"
  keep_locally = false
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.image_id
  name  = "tutorial"

  ports {
    internal = 80
    external = 8000
  }
}
 ```

Review the configuration
The set of files used to describe infrastructure in Terraform is known as a Terraform configuration.

Each Terraform configuration must be in its own working directory. You created a working directory previously in learn-terraform-docker-container. Review the main.tf file.

This is a complete configuration that you can deploy with Terraform. In this tutorial, you will learn about each block of this file you deployed previously in more detail.

Terraform Block
The terraform {} block contains Terraform settings, including the required providers Terraform will use to provision your infrastructure. For each provider, the source attribute defines an optional hostname, a namespace, and the provider type. Terraform installs providers from the Terraform Registry by default. In this example configuration, the docker provider's source is defined as kreuzwerker/docker, which is shorthand for registry.terraform.io/kreuzwerker/docker.

You can also set a version constraint for each provider defined in the required_providers block. The version attribute is optional, but we recommend using it to constrain the provider version so that Terraform does not install a version of the provider that does not work with your configuration. If you do not specify a provider version, Terraform will automatically download the most recent version during initialization.

To learn more, reference the provider source documentation.

Providers
The provider block configures the specified provider, in this case docker. A provider is a plugin that Terraform uses to create and manage your resources.

You can use multiple provider blocks in your Terraform configuration to manage resources from different providers. You can even use different providers together. For example, you could pass the Docker image ID to a Kubernetes service.

Resources
Use resource blocks to define components of your infrastructure. A resource might be a physical or virtual component such as a Docker container, or it can be a logical resource such as a Heroku application.

Resource blocks have two strings before the block: the resource type and the resource name. In this example, the first resource type is docker_image and the name is nginx. The prefix of the type maps to the name of the provider. In the example configuration, Terraform manages the docker_image resource with the docker provider. Together, the resource type and resource name form a unique ID for the resource. For example, the ID for your Docker image is docker_image.nginx.

Resource blocks contain arguments which you use to configure the resource. Arguments can include things like machine sizes, disk image names, or VPC IDs. Our providers reference documents the required and optional arguments for each resource. For your container, the example configuration sets the Docker image as the image source for your docker_container resource.

Initialize the directory
When you create a new configuration — or check out an existing configuration from version control — you need to initialize the directory with terraform init.

Initializing a configuration directory downloads and installs the providers defined in the configuration, which in this case is the docker provider.

If you did not deploy the Quick Start steps in the previous tutorial, initialize the directory now.

```
terraform init
```
Terraform downloads the docker provider and installs it in a hidden subdirectory of your current working directory, named .terraform. The terraform init command prints out which version of the provider was installed. Terraform also creates a lock file named .terraform.lock.hcl which specifies the exact provider versions used, so that you can control when you want to update the providers used for your project.

Create infrastructure
Apply the configuration now with the terraform apply command. Terraform will print output similar to what is shown below. We have truncated some of the output to save space.

```
terraform apply
```
Before it applies any changes, Terraform prints out the execution plan which describes the actions Terraform will take in order to change your infrastructure to match the configuration.

The output format is similar to the diff format generated by tools such as Git. The output has a + next to docker_container.nginx, meaning that Terraform will create this resource. Beneath that, it shows the attributes that will be set. When the value displayed is (known after apply), it means that the value will not be known until the resource is created. For example, Docker assigns a random ID to images upon creation, so Terraform cannot know the value of the id attribute until you apply the change and the Docker provider returns that value.

Terraform will now pause and wait for your approval before proceeding. If anything in the plan seems incorrect or dangerous, it is safe to abort here with no changes made to your infrastructure.

In this case the plan is acceptable, so type yes at the confirmation prompt to proceed.

```
Enter a value: yes

docker_image.nginx: Creating...
docker_image.nginx: Still creating... [10s elapsed]
docker_image.nginx: Creation complete after 13s [id=sha256:d1a364dc548d5357f0da3268c888e1971bbdb957ee3f028fe7194f1d61c6fdeenginx:latest]
docker_container.nginx: Creating...
docker_container.nginx: Creation complete after 2s [id=2834ad6283372ceb61121739ce71d31cb0237ad50f4dc234e3445c9445439181]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```
You have now created infrastructure using Terraform!

To confirm your Image you can as well Type
```
 docker run -p 8080:80 --name nginx nginx
```

Visit localhost:8000 in your web browser to verify the container started.

Inspect the current state using terraform show.
```
terraform show
```
We have Successfully Created a Docker Container Using Terraform
