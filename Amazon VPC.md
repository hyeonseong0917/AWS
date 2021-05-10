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

Your VPC will launch a NAT Gateway to provided Internet access to private resources.

the NAT Gateway will be assigned a static IP address, Known as an Elastic IP address.

In this task, you will create the Elastic IP address.

you can associate the Elastic IP address with a resource in your VPC, such as a NAT Gateway

or an Amazon EC2 instance.



In the AWS Management Console click VPC.

click Elastic IPs.

click Allocate Elastic IP address

click Allocate

#### your Elastic IP address is displayed. you will use it in the next task



## Task 2: Create an Amazon VPC

create an Amazon VPC using the VPC wizard.

The wizard automatically creates a VPC based upon parameters you specify.



#### Here is an overview of the VPC you will create:

![overview](https://user-images.githubusercontent.com/83209355/117619998-f3eec400-b1aa-11eb-9897-22e714b7eab3.png)




click VPC Dashboard

click Launch VPC Wizard

> The wizard offers four pre-defined configurations.
>
> > 1.VPC with a single Public Subnet: connected to the Internet
> >
> > 2.VPC with Public and Private Subnets:  A public subnet for Internet, private subnet for back-end resources.
> >
> > 3.VPC with Public and Private Subnets and Hardware VPN Access: A public subnet and a private subnet, plus a Virtual Private Network connection to an existing Corporate Data Center.
> >
> > 4.VPC with a Private Subnet Only and HardWare VPN Acess: A private subnet connected to a Corporate Data Center via a VPN connection.



##### This lab will use the VPC with Public and Private Subnets template.

click Create VPC







## Task 3: Explore your VPC

explore VPC components created by the VPC wizard



Select a VPC field, then click "My VPC"

click Internet Gateways.

>  The Internet gateway for your VPC will be displayed

click Subnets.

> A subnet is a subset of a VPC.
>
> > Belongs to a specific VPC
> >
> > Exists in a single Availability Zone (while a VPC can span multiple AZ)
> >
> > Has a range of  IP addresses (CIDR range)



Select Public subnet

> Each subnet is assigned a unique Subnet ID.
>
> The IPv4 CIDR of  10.0.25.0/24 means that the subnet contains the range of IP addresses from 10.0.25.0 to 10.0.25.255.
>
> The subent only has 250 Available Ips



click the Route table tab

Each subnet is associated with a Route table, which specifies the routes for outbound traffic leaving the subnet. 


![subnet-public](https://user-images.githubusercontent.com/83209355/117620143-20a2db80-b1ab-11eb-892b-7a03b163f916.png)


There are two routes in the route table that is associated with your public subnet.

> ##### Route 10.0.0.0/16 | local
>
> > directs traffic destined for elsewhere in the VPC

> ##### Route 0.0.0.0/0 | igw-
>
> > directs all traffic to the Internet gateway

> Traffic is first sent within the VPC if it falls within the range of the VPC,
>
> otherwise it is send to the Internet.
>
> This subnet is associated with a Route Table that has a route to an Internet gateway makes it a Public Subnet. That is, it is reachable from the Internet.



click the Network ACL tab

>  A network access control list (ACL) is an optional layer of security for your VPC that acts as a firewall for controlling traffic in and out of subnets.
>
> Network ACLs are normally left with their default settings:
>
> > Rule 100 Inbound : allows all inbound traffic into the Public Subnet.
> >
> > Rule 100 outbound : allows all traffic out of the Public Subnet.

![nacl](https://user-images.githubusercontent.com/83209355/117620209-36b09c00-b1ab-11eb-9b7f-599d70f94b10.png)




select Private subnet



click the Tags tab.

> tagged with the key of Name having the value of Private subnet.
>
> 

click the Route Table tab.

> Route Table for the Private subnet has the configuration:
>
> > ###### Route 10.0.0.0/16 | local
> >
> > > same as the Public subnet.
> >
> > ##### Route 0.0.0.0 | nat-
> >
> > > directs traffic to the NAT Gateway.

> This subnet does not have a route to the Internet Gateway. Therefore, it is a Private subnet.
> 
![subnet-private](https://user-images.githubusercontent.com/83209355/117620277-4cbe5c80-b1ab-11eb-9b99-48c57b09f4f1.png)




click NAT Gateways.

> NAT Gateway allows resources in a private subnet to connect to the Internet and other resources outside the VPC. This is an outbound-only connection, which means that the private subnet. Resources on the Internet  cannot initiate an inbound connection.
>
> Therefore, it is a means of keeping resources private and improving security for VPC resources.

![nat-gateway](https://user-images.githubusercontent.com/83209355/117620330-5f389600-b1ab-11eb-8f14-f2dc5abfe33e.png)



# Conclusion

you now have successfully

> created an Amazon VPC using the VPC Wizard
>
> Explored the basic components of a VPC







