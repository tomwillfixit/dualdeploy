# Dual Deployment

Disclaimer : No actual services will be deployed in this example.  This code is simply to demonstrate the flow. 

## What is a dual deployment?

Deploying the same service code in different formats.  In this example our service is installed on a EC2 instance (Centos 7) and can handle traffic using the VM interface.  The same code is containerized and available to handle traffic using the host (VM) interface.  Currently we manually redirect traffic for the purposes of testing.

## Building a dual deployment environment using Packer

The Packer template [aws.json](aws.json) is used to build a Amazon Machine Image. This image will be bootstrapped, our service will be installed as an RPM and our service container built. 

The bootstrap.sh can be as simple or complex as you need it to be.  We use it to install some helper tools and install Docker.

The container.sh pulls the service container code from github and builds the container images as part of the AMI build.  

The aws.json template supports 6 arguments :

```
    base_image : The AMI ID of the base image you want to build from.
    new_image  : The name of the new AMI you are building.
    access_key : AWS access key 
    secret_key : AWS secret key
    script     : The name of the script you want to run to bootstrap the base image.
    script_args: Pass in additional arguments to the script.
```

Create a Primed Centos 7 AMI which includes container build provisioner :

```
packer build -var 'base_image=ami-c7d092f7' -var 'new_image=CentOS-7-x86_64 (Demo)' -var 'script=bootstrap.sh' -var 'access_key=12345674345456454' -var 'secret_key=erewrwrw2424esfsfdfwrwerwe2423wer' -var 'script_args=' aws.centos.json
```

When the build command completes successfully you will have an AMI with the service installed and with the service container built.

## What's the point ?

Our CD pipeline has supported AMI builds and RPM based service installs for a while.  We wanted to run a single containerized service with as little disruption to the existing pipeline as possible.  By tweaking the Packer Templates we had a simple, low risk way to get our containerized services into Production.  

## Summary

We are currently at the point of testing our containerized services in these 'dual deploy' environments. Some of the fear and stigma of putting containers into Production has been removed.  We understand the deployment of containers better and we are piggy backing a well defined CD pipeline.  
So often the first step is the hardest and this approach has given us a low risk, less intimidating way of getting our services closer to a containerized future in Production.  


