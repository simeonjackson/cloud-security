<h1>Cloud Security in Azure</h1>
This tutorial outlines the implementation of some common security settings within Microsoft Azure that would be typical in an enterprise environment such as network security groups, multi-factor authentication, and role-based access. <br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Entra ID)
- Remote Desktop
- Active Directory
- Access Control (IAM)

<h2>Operating Systems Used </h2>

- Windows 11
- Windows Server 2022

<h2>Deployment and Configuration Steps</h2>

<p>
I wanted to imitate an organization setting up cloud security rules in Azure. To used the lab environment I made in my Active Directory lab.

The first think I did was review and configure a network security group (NSG) for my resource group. I have a NSG for my domain controller called, "DC1-nsg", it currently allows all outbound traffic and RDP access through port 3389.

</p>

![Screenshot 2025-03-19 082431](https://github.com/user-attachments/assets/c15111b9-5ecd-4f80-8330-090de9fcd426)


<p>
I want to set up a rule that will only allow inbound RDP access from IP addresses in my subnet.  The subnet for this network is 10.0.0.0/24 which can be found by looking under the virtual network in your resource group. 

Back in my NSG I made an inbound security rule setting the source IP addresses to be the IPs in my subnet. I left the destination as any by default and set the service to RDP because that is the traffic I am trying to restrict. Lastly I chose "allow" as I want to allow traffic from my selected set of IP addresses. The priority is set at 300, the lower this number the higher priority the rule will be. This comes in handy when creating multiple rules in a NSG.

</p>

![Screenshot 2025-03-19 083715](https://github.com/user-attachments/assets/c1149005-9b0d-4f66-abed-4a2d3d88e2a1)


<p>
After creating this rule I applied the change to the subnet by going back to the virtual network, selecting the subnet where my VMs exist and adding the NSG. This will apply the rules to all VMs that are currently in the subnet and those that I may add in the future.
</p>

![Screenshot 2025-03-19 084513](https://github.com/user-attachments/assets/4f902615-ab6e-4f52-9aac-9c04924781d2)


<p>
Next, I wanted to ensure that Multi-Factor Authentication (MFA)  is applied to my environment. MFA provides an extra layer of security and protection against password breaches and it is also a compliance requirement for many common frameworks companies use (HIPAA, PCI-DSS, NIST, etc.)

Ideally we would create a conditional access policy to enforce MFA based on conditions such as: user or group membership, location, device, application, or sign-in risk. A conditional access policy will allow you to select users or groups that the policy applies to as well as select applications that the policy can protect. Certain conditions can be applied as well such as requiring MFA outside of a trusted network location. A conditional access policy can be made by searching for Microsoft Entra Conditional Access. However I do not have Microsoft Entra ID Premium so I cannot used this feature.
</p>

![Screenshot 2025-03-19 172511](https://github.com/user-attachments/assets/c2bd30b1-62bb-462e-a6ae-8a40776b16f5)


<p>
Navigating to Microsoft Entra ID and clicking the Properties tab in the left-hand column I can see that Azure has enabled security defaults which includes MFA as a feature. It also mentions that 99.9% of account compromise can be stopped by using MFA.
</p>

![Screenshot 2025-03-19 173031](https://github.com/user-attachments/assets/117ddd88-d14a-4427-bece-71be5315affa)


<p>
I can create a local user named, MFATest, to show that MFA is enforced by default in this environment.
</p>

![Screenshot 2025-03-19 180121](https://github.com/user-attachments/assets/fe2032e5-a174-44f3-a049-08d08077b593)



<p>
Logging in to Azure Portal with MFATest I am prompted that my organization requires additional security information before gaining access. In this case I will be asked to set up the Microsoft Authenticator app on my phone. There are other authentication apps but they all work the same.
</p>

![Screenshot 2025-03-19 180503](https://github.com/user-attachments/assets/7e7e2491-ca71-447b-877b-a73f1c7d47fd)


<p>
Once the app is installed I will connect the Microsoft Authenticator app with my account by scanning the QR code on the screen. There will be one more screen asking you to approve the notification sent to your app by entering a number. Once this is done, MFA is set up for our test user. Now when logging in I will be prompted to approve my logins using the app on my phone creating an extra layer of security.
</p>

![Screenshot 2025-03-19 181326](https://github.com/user-attachments/assets/5defdc83-4cd4-42d2-9d97-8e9d7c03d11c)


<p>
Next I will look at Role-Based Access Control (RBAC) which is used to regulate access to computer resources based on roles of users or groups within an organization. RBAC is used to enforce the very popular principle of least privilege which increases your networks security by reducing the risk of unauthorized access.

In Azure RBAC can be applied at the subscription, resource group, or individual resource level and these permission can be given to individual users, groups, or services. The key to RBAC is assigning roles, which are premade by Azure but you can also create your own.

I will apply RBAC to my resource group, Active Directory. These controls can be found in the Access Control (IAM) tab in the left-hand column. To assign a role I clicked Add and then Add Role Assignment.

</p>

![Screenshot 2025-03-19 203647](https://github.com/user-attachments/assets/32d89cf6-e8ed-4dee-8bda-32c3f9178448)


<p>
Azure comes with hundreds of built in roles and has descriptions of what they are used for. The category section can also be useful to for groups. Clicking details will give you a list of all the permissions associated with that role.
</p>

![Screenshot 2025-03-19 204341](https://github.com/user-attachments/assets/faa08540-0982-45d5-ac45-5535d6d5e981)


<p>
Azure also has Privileged administrator roles that can be used to manage all resources, policies, and assign roles to other users. Ideally these roles are used sparingly as most job functions can be done with less access.
</p>

![Screenshot 2025-03-19 204741](https://github.com/user-attachments/assets/1495843d-8d45-495a-891a-98e899505f91)


<p>
For the sake of this lab I assigned our test user with the Reader role this will allow this user to view all resources but not allow the user to make any changes.
</p>

![Screenshot 2025-03-19 205306](https://github.com/user-attachments/assets/2fca2e64-5787-4a2e-a7c6-e2bd1a571c61)


<p>
Back on the IAM page I can go to role assignments to see the roles assigned in my resource group. I am listed as an owner role which means I have access to manage all resources in the group. We can verify that our test user has a role of reader which we just set.
</p>

![Screenshot 2025-03-19 205947](https://github.com/user-attachments/assets/7b15f9b4-0d9f-4f92-bee4-dcb6bfbab6be)


<p>
These are three very common ways that I have learned to secure an Azure environment. Security is obviously paramount to any organization and a defense in depth model is the way to go. Making sure users only have access and privileges to do what they need to do and using a form of privileged identity management are ways that you can downsize risk to your organization. These controls should be monitored and audited frequently to ensure they are still appropriate.
</p>
