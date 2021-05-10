# Introduction to Amazon Virtual Private Cloud (VPC)



## Overview

1.use Amazon VPC wizard to create a VPC

2.attatch an Internet Gateway

3.add a subnet and then define routing for the VPC so that traffic

   can flow between the subnet and the internet gateway



## Topics covered

Upon completion of this lab, you will be able to:

> Create an Amazon VPC using the VPC Wizard
>
> Explore the basic components of a VPC including:
>
> > 1.Public and private subnets
> >
> > 2.Route tables and routes
> >
> > 3.NAT Gateways
> >
> > 4.Network ACLs
> >
> > 5.Elastic IPs



## What is Amazon Virtual Private Cloud(VPC)?

lets you provision a logically isolated section of the AWS cloud where you can launch

AWS resources in a virtual network that you define.



## Task 1: Create an Elastic IP address

Your VPC will launch a NAT Gateway to provied Internet access to private resources.

the NAT Gateway will be assigned a static IP address, Known as an Elastic IP address.

In this task, you will create the Elastic IP address.

you can associate the Elastic IP address with a resource in your VPC, such as a NAT Gateway

or an Amazon EC2 instance.



## Task 2: Create an Amazon VPC

create an Amazon VPC using the VPC wizard.

The wizard automatically creates a VPC based upon parameters you specify.



## Task 3: Explore your VPC

explore VPC components created by the VPC wizard



# Conclusion

you now have successfully

> created an Amazon VPC using the VPC Wizard
>
> Explored the basic components of a VPC







