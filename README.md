# Securing Network Posture with AWS VPC Network Access Analyzer

 ### [YouTube Demonstration](https://youtu.be/7eJexJVCqJo)

<h2>Description</h2>
This project involved analyzing and configuring network architectures across three preconfigured virtual private clouds (VPCs) in Amazon Web Services (AWS). Each VPC contained essential network components, including EC2 instances, subnets, route tables, network access control lists (ACLs), and security groups. The project’s focus was to understand the network design, analyze traffic paths, and ensure secure communication between resources by updating security configurations.<br />

<h2>Languages and Utilities Used</h2>

- <b> JSON: Used for configuring Network Access Scopes and security groups.</b>
- <b> AWS Management Console for configuring and managing resources.</b>
- <b> Amazon EC2 for the instances running within the VPCs.</b>
- <b> Amazon Virtual Private Cloud (VPC) for creating and managing the network architecture.</b> 
- <b> Security Groups, NACLs, and Route Tables for controlling traffic flow</b>
- <b> Network Access Analyzer for analyzing and verifying network security posture.</b> 
- <b> NAT Gateway for managing internet traffic from private subnets.</b>

<h2>Environments Used </h2>

 <b>Cloud Platform:
AWS (Amazon Web Services): This project was conducted entirely in AWS, utilizing several of its services.
</b> 
<h2>Program walk-through:</h2>

<p align="center">

Figure 1: Three Amazon EC2 instances in VPC A: The App Server instance is an application server that resides in the private subnet. The Bastion Host instance is used to connect to internal, protected resources, such as App Server. The Public Server instance is a test server. It is used to test whether Secure Shell (SSH) traffic is allowed into App Server from any hosts besides the Bastion Host instance. The Public Server instance resides in the public subnet.
 <br/>
![Before Image of three Amazon EC2 instances](https://github.com/user-attachments/assets/709b8d1d-e50b-452e-95c7-0b28c7ca7d46)

<br />

Review App Server Inbound Rules: This configuration allows inbound connections to App Server from any IP address over port 22. All other ports are denied access. This allows Bastion Host to connect to App Server. However, unauthorized hosts from any IP address (source 0.0.0.0/0) can also connect to App Server. Currently, this security group is noncompliant based on the company security policy.
 <br/>
![Review App Server Inbound rules](https://github.com/user-attachments/assets/4a9c9c4f-6128-4202-97db-4c38b0a405cb)



<br />

Figure 2: The user can access the Bastion Host and Public Server instances. From there, the user can connect by using SSH into the App Server instance from either of the public Bastion Host or Public Server instances.
 <br/>
![Figure 2 Test Connectivity](https://github.com/user-attachments/assets/d9b619a7-95f6-46fc-8376-19de3b0b639a)

<br />

I was able to connect to the App Server using the Bastion Host instance:
 <br/>
![Successful SSH to App Server from Bastion Server](https://github.com/user-attachments/assets/f3de6f20-45bb-42d4-812e-0c202bd42345)


<br />

I was able to connect to the App Server using the Public Server instance:
 <br/>
![Successful SSH to App Server from Public Server](https://github.com/user-attachments/assets/770bfe89-e564-4b5d-8cfb-4b856cc46d66)


<br />

Figure 3: Updating the App Server security group allows traffic only from the Bastion Host as a source. 2. This blocks the connection attempts from the Public Server.
 <br/>
![Figure 3 Restricting Access to Public Server](https://github.com/user-attachments/assets/78575ae0-76d1-4e1d-8efd-bb0ee35e37fd)


<br />

Edit Inbound Rules to only allow traffic from the Bastion Host:
 <br/>
![Update Security Group Rules for App Server from Bastion Server Only](https://github.com/user-attachments/assets/37193e94-fab7-4bd0-809b-0ca12beff73f)

<br />

Unable to connect to the App Server from the Public Server after updating the inbound rules: This is compliant behavior.
 <br/>
![Unsuccessful SSH to App Server from Public Server](https://github.com/user-attachments/assets/6c8c230b-32db-4805-9b34-62d08708b8d1)


I checked for connectivity to the App Server from the Bastion Host after updating the inbound rules: It was successful. This is compliant behavior.
 <br/>
![Successful SSH to App Server from Bastion Server](https://github.com/user-attachments/assets/c42c1a6b-0401-4661-ab9d-cc2997386aa2)

<br />

Figure 4: I turned the Public Server into another Bastion Host, and added it to the BastionHostSG security group. The user can use the Bastion Host or the second Bastion Host (Public Server) as a compliant way to connect by using SSH into the App Server. Both the Bastion Host and Public Server are assigned the BastionHostSG.
 <br/>
![Figure 4](https://github.com/user-attachments/assets/d1805b90-76e2-4429-a8ca-cd1ba4590422)


<br />

I updated the security group attached to App Server so that it only allows traffic from instances with the BastionHostSG attached:
 <br/>
![Update Security Group Rules for App Server from Bastion Server Instances](https://github.com/user-attachments/assets/3625377c-82ec-4291-a1f4-049606b191ed)


<br />

I checked for connectivity to the App Server from the Public Server after updating the security group: It was successful. This is now compliant behavior because you made the Public Server an additional bastion host by adding it to the Bastion Host security group.
 <br/>
![Successful SSH to App Server from Public Server after adding to Bastion SG](https://github.com/user-attachments/assets/823855de-93fc-403d-8226-288db782e42d)


<br />
<h2>Challenge: Troubleshooting connectivity within the VPC</h2>
<br />

Figure 5: The Apache server is running on an EC2 instance. The EC2 instance needs the correct security group rules to allow a connection through the internet. This shows a sample test page or a custom html page to show that you have connectivity to your Apache server.
 <br/>
![Figure 5](https://github.com/user-attachments/assets/d0ad2e3d-c2be-449d-8e2b-d74d064379e3)


<br />

Here are the inbound rules for the Apache Server prior to me testing connectivity using http://3.8.111.1
 <br/>
![Inbound Rules before testing connectivity  to Apache Server](https://github.com/user-attachments/assets/5fabef28-8431-4afa-8e98-f2fe1897b570)
<br />

I was not able to connect to the Apache server using http://3.84.111.1:
 <br/>
![Unsuccessful Apache Server](https://github.com/user-attachments/assets/8504963f-0c90-4e43-a2a1-cb9b5088e04d)
<br />

I suspected issue was that the instance need to allow traffic from "HTTP" port 80 instead of "HTTPS" port 443. I made configuration changes to address to address the issue.
 <br/>
![image](https://github.com/user-attachments/assets/ad991e31-c5c8-481f-846e-6b2eb9198d82)

<br />

I refreshed the browser and I was able to connect to the Apache test page.
 <br/>
![Successful connection to Apache Server](https://github.com/user-attachments/assets/f5e74d12-4a1e-4ab9-a1ec-8e9cb2585717)

<br />

<h2>Summary</h2>
The project comprised tasks aimed at enhancing network security and optimizing resource connectivity across multiple VPCs. Key tasks included using Network Access Scope templates to analyze inbound traffic, verifying the use of NAT gateways for internet traffic, and configuring custom Network Access Scopes to assess private subnets and VPC segmentation. The final step involved updating security groups to match required security policies, ensuring controlled access between VPC resources.
<br />



