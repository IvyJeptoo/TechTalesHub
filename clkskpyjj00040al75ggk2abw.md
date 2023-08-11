---
title: "Infrastructure-as-Code: Networking Infrastructure."
datePublished: Tue Aug 01 2023 17:29:09 GMT+0000 (Coordinated Universal Time)
cuid: clkskpyjj00040al75ggk2abw
slug: infrastructure-as-code-networking-infrastructure
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1690912584419/769ef275-4521-43fc-ba09-3b42ff62885f.png
tags: aws, amazon-web-services, networking, infrastructure-as-code, iac

---

I have been deep in the abyss of learning Infrastructure as Code in Cloud computing and through my Udacity classes I have had several exercises and I thought why not document a solution to one :-)

Infrastructure Diagrams are used as an interpretation of the visual requirement of the cloud's infrastructure before it is translated into code. Think of it as a blueprint of a building, but instead of showing rooms and walls, it shows servers, computers, networks, and other devices. It can also include things like databases, cloud services, and internet connections.

Below is an example of a diagram that we will be using:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1690824450144/adf357d3-588e-4bca-a508-be6de850ef7e.png align="center")

### Diagram components.

**a) AWS Cloud**

This is your AWS account and every other resource it can get access to.

**b) Virtual Private Cloud(VPC)**

The VPC acts as a private network and it provides a private IP address for networking resources. We can think of it as a logical container for all the resources that we will be creating. The IP address range is 10.0.0.0/16 meaning that it can accommodate up to 65,536 IP addresses.

**c) Internet Gateway**

An internet gateway is a bridge between the VPC and the internet in the sense that it is the entry and exit point for traffic between the two. It does this by allowing resources within the VPC to communicate with the internet and also receives incoming traffic from the internet.

**d) Public Subnet**

Public subnets receive traffic from everywhere on the server, which means the instances placed in this subnet have public IP addresses and can be accessed directly from the internet. It has an IP address range of 10.0.1.0/24 which allows up to 256 IP addresses.

**e) Private Subset**

Just as the Public subset the private subset accommodates up to 256 IP addresses but it has an IP address range of 10.0.0.2.0/24. It is normally used when hiding internal resources from the internet since the instances in this subset do not have public IP addresses.

**f) NAT Gateway.**

The NAT(Network Address Translation) Gateway provides ways in which the private network and server communicate it does this by allowing the private subset to communicate with the internet and at the same time prevents incoming connections from the internet. It acts as a middleman that handles communication.

**g) Public Route Table**

Route Tables contain a set of rules that determine the direction of the network traffic within the VPC. The Public Route Table is associated with the Public Subnet and has a route that sends internet-bound traffic to the Internet Gateway. Public Route Table allows the direct reach of the Public Subnet Instances to the Internet.

**h) Private Route Table**

Unlike the Public Route Table, the Private is associated with the Private Subnet and has a route that sends internet-bound traffic to the NAT Gateway. By this, the Private Route Table ensures that the Private Subnet Instances have access to the Internet through the NAT Gateway.

### Diagram Flow

When a user or another server wants to connect to the resources in the VPC, the Public Subnet is targeted because it has instances with public IP addresses which makes them reachable from the outside world.

Public Subnet Instances can send outbound traffic directly to the internet without any configurations, this is made possible by the public IP address.

The Private Subnet Instances are isolated from direct internet access making them secure for sensitive applications or data. The Instances however need to access the internet for updates, APIs, or services and this is where the NAT Gateway comes into play. It enables internet access for Instances in the Private Subnet.

The NAT Gateway receives outbound traffic from the Private Subnet Instance and sends it to the internet, the origin of the traffic will be seen as the NAT gateway and not the Private Subnet.

Traffic from the internet is not allowed to connect with the Private Subnet Instances to protect sensitive resources and data from unauthorized access.

The Private Route Table directs internet-bound traffic to the NAT Gateway ensuring that the Private Subnet Instances can access the internet through the NAT Gateway. The Public Route Table is for directing internet-bound traffic to the Internet Gateway, allowing Public Subnet Instances to have direct access to the Internet.

### Implementation Steps

* We will start by creating a VPC and attaching the VPC to the Internet gateway through the Internet Gateway Attachment and also create the public and private subnet within the VPC. Below is how our YAML file will be with the VPC and Internet Gateway created:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1690887930460/0449de12-4ca0-4a7f-b8cf-c63735d5c143.png align="center")
    
    The parameter file will look like so:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1690888145308/2028761f-ce3a-4254-a544-3b909b502116.png align="center")
    
* We need to deploy a NAT gateway in the public subnet this requires an allocation of the Elastic IP that will be used to assign it to the NAT Gateway. You can read more on [MapPublicIpOnLaunch](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-subnet.html#cfn-ec2-subnet-mappubliciponlaunch)
    
* Below is how our YAML file looks with the subnet, NAT Gateway, and Elastic IP added:
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1690892541140/b61ee092-64ef-490f-84f5-9933701f0af6.png align="center")
    
* Next, we are going to create the Public and Private Routing Table and assign it to the corresponding Subnet. The public Route Table sends default traffic to the Internet Gateway while the private Route Table sends traffic to the NAT Gateway.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1690896015924/5bf7df33-09b2-402a-929b-c354cb3e9a40.png align="center")

On the AWS console this is how our resources look like:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1690906368324/31b8b127-9632-4604-ab45-7745ced58c31.png align="center")

* That is basically it!! You should be able to delete the cloud formation script and create it over again a summary definition of Infrastructure-as-Code.
    

Check out the whole repository for this article [here](https://github.com/IvyJeptoo/network-infrastructure/settings)