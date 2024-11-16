<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Build an Active Directory</h1>
Active Directory (AD) is a Microsoft-developed directory service that helps manage users, devices, and network resources on Windows domain networks. In this project, we will use two VMs, one as the server and the other as the client. In an enterprise scenario, the client would be like the computer an employee would log in to as their user to operate.<br />
<br>
The link to the VM project to build a virtual machine is https://github.com/brandenoz/virtual-machine. 

<h2>Requirements</h2>
- Good internet connection.
- Microsoft Azure account unless using physical PCs. 

<h2>Configuration Steps</h2>
<h3>Step 1: Set Up Virtual Environment</h3>

Begin by opening Microsoft Azure. Make sure that all resources are set to the same region. I will be using East US. When we are done with this step, our resources will look something like this: (see screenshot). 
<br>

![1](https://github.com/user-attachments/assets/02c48e8d-b9b3-4174-9cf7-6067fbc79ec4)

- Create a Resource Group named Active-Directory-Lab. 
- Create a Virtual Network named Active-Directory-VNet. (make sure 
- Create a Virtual Machine (computer) named DC-1. Choose for the image Windows Server 2022 Datacenter. Choose for the size at least 2 VCPUs as less could make it slow to operate. Set your username and password to something that you can easily remember. Record the credentials on a saved note. I will be using labuser and Cyberlab123! as credentials. 
- Under Networking, choose the virtual network the one we created, Active-Directory-VNet. 
- Create a new virtual machine for the client/employee computer named Client-1. Choose for the image Windows 10 Pro. For the size choose at least 2 VCPUs which it might default to. Use the same credentials for simplicity. Make sure to check the box under licensing before moving beyond this page. This is very important. For Networking, make sure to use the virtual private network previously created. 
- In VM DC1, change the IP address in the Virtual Network Interface Card, VNIC to be static. <br>

![2](https://github.com/user-attachments/assets/b00587c8-d6ed-44e9-b3bd-513df62c8a2b)

<h3>Step 2: Disable Firewall</h3>

Get the Public IP Address of the VM DC-1. Use Remote Desktop Connection to log in to our domain controller. 
- Right-click the Start menu and select Run. Type wf.msc for Windows Firewall. Click on Windows Defender Firewall Properties. Next to Firewall State: switch to off. Do this for Domain Profile, Private Profile, and Public Profile, then Apply > Okay. 
- Change the DNS settings of VM Client-1’s Virtual Network Interface Card to be custom rather than dynamic. We will paste the private IP Address of DC-1. This effectively points Client-1’s DNS to DC-1. Save. <br>

![3](https://github.com/user-attachments/assets/59615359-0b0b-481b-9642-d6c730e1b016)

- Restart Client-1. Login to Client-1 using the Public IP Address and Remote Desktop Connection. 
- Open Windows PowerShell and type ping 10.0.0.4 to make sure you can make a connection. <br> 

![4](https://github.com/user-attachments/assets/118bde18-4625-491f-82d5-f4fc69698342)

<h3>Step 3: Make the Server a Domain Controller</h3> 

In Server Manager, click Add Roles and features. Click Next > Next > Next > Active Directory Domain Services > Add Features > Next > Next > Next > Restart the destination server automatically if required > Yes > Install > (once installed) Click Close. 
- Click the flag in the upper right corner of Server Manager on DC-1, and click Promote this Server to a Domain Controller. 
- Click Add a new forest > Type in the Root domain name: mydomain.com > Next > Password: Cyberlab123! > Next > Check and uncheck "Create DNS delegation" then click Next > Next > Next > Next > Install (wait to be installed). This action will cause the VM to restart. You will have to log back in after. <br>

![5](https://github.com/user-attachments/assets/cf09c896-7580-447b-b413-7985fad6643f)

<h3>Step 4: Log In to Domain Controller</h3> 

Not that DC-1 is a Domain Controller, logging in requires context to be specified. 
- Using Remote Desktop Connection and the VM’s public IP address. 
- For the username: “mydomain.com\labuser”. <br>

![6](https://github.com/user-attachments/assets/e854c6a8-c7ea-4de3-b596-e99df6545fd9)

Our server, DC-1, is now a Domain Controller, and we are logged back into DC-1. 

<h3>Step 5: Create a Domain Admin, Jane</h3> 

Click Start and click on Active Directory Users and Computers.
- Put Jane Doe with user logon name as jane_admin and click next.  
- Right-click on Jane Doe, properties > member of > add > Domain Admins (check names) > OK > Apply > OK. 
- Now logout of this admin account. <br>

![8](https://github.com/user-attachments/assets/7d5ae4a9-a223-47f2-9938-3fc9bf3495a8)

<h3>Step 6: Change the Domain of Client-1 to mydomain.com</h3>

- Log into DC-1 as Jane Admin. 
- Log into Client-1 as labuser. 
- Right-click on the Start icon and click about > Make full screen > Click on Rename this PC (advanced) > Under the Computer Name tab click Change... > Select domain under Member of and type: mydomain.com > Click OK > Click Apply > Click OK. 
- This will require the PC to restart. Go ahead and let it restart. Log back in afterward. <br>

![9](https://github.com/user-attachments/assets/74ef22e5-a148-4cc1-8aa2-9074a9e04b4c)

<h3>Step _: Verify Domain Change</h3> 

- Go back into DC-1, and Go to Active Directory Users and Computers. 
- Click on mydomain.com > Computers > double-click Client-1 to open properties > Observe under Operating System that this computer is running Windows 10 Pro. <br>

![10](https://github.com/user-attachments/assets/7d3ba860-d36e-4b1e-957a-ce8a99eedef9)

- Create a new Organizational Unit called _CLIENTS. 
- Drag Clinet-1 from Computers to _CLIENTS > click Yes. Right-click on mydomain.com and click Refresh. 

<h3>Step _: Allow Remote Access for Employees to Client-1</h3>

- In Client-1, the employee computer, right-click the Start Menu and go to System, make full-screen, and click on Remote Desktop > Click Select users that can remotely access this PC > Click Add > Type: "Domain Users" (check names) > OK > OK. 

<h3>Step _: Create Users</h3>

- Open DC-1 as Jane admin. 
- Open Windows PowerShell ISE as an Admin. Click on Scripts in the upper-right corner to open a script box. 
- Go to this link to copy a script for Powershell that will create users: https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1. 
- Paste the script into the white script area 
- (optional) In line 3 of the script, you can see 10,000 users is the amount set. You can change this to 100 to make this process a bit shorter. 
- Click the green button for Run Script. <br>

![11](https://github.com/user-attachments/assets/51e7b6c0-7360-45c2-bed4-ea3f98f5e730)

- Go back to the Active Directory, refresh mydomain.com, and click on _EMPLOYEES. Choose an employee name that will be easy to remember. If you are following this lab, my users will be completely different than yours. So whichever I choose will not likely be an option for you so choose one that is easy to remember. 
- I will choose dog.fomi because this one is silly. <br>

![12](https://github.com/user-attachments/assets/f681abbe-d96c-4767-bcf2-10108284514d)

- Log out of Client-1. Log back in with your selected employee user, mine is dog.fomi. Username: mydomain.com\dog.fomi, password is Password1. 

<h3>Login as Employee</h3>

Once logged into Client-1 as user dog.fomi, open Command Prompt. You will see that the use is registered in Command Prompt as dog.fomi. 
- Open File Explorer, go to the C drive, users, and observe dog.fomi as an accessible folder. Also, notice that other folders may not be able to be opened without admin access, and dog.fomi is not an admin. 
- Logout. 

<h3>Update Group Policy for Account Lockout after Password Attempt Failure</h3>

Click Start, type run, press Enter, and type gpmc.msc, press Enter. 
- Click the drop-down under Forest: mydomain.com, then Domains, then mydomain.com, right-click on Default Domain Policy, and click on Computer Configuration. <br>

![13](https://github.com/user-attachments/assets/76d26f42-f3c6-4aac-8bd0-f097fb76b0bd)

- Click the drop-down under Policies, then for Windows Settings, Account Policies, and Account Lockout Policy. 
- Set the lockout duration to 30 minutes. 
- Make sure the Account lockout threshold is set to 5 invalid logon attempts. 
- To see the updated Group Policy go to the Group Policy Management window, click Default Domain Policy, scroll down to Computer Configuration and you will see the settings we adjusted for password lockout. (see screenshot) <br>

![14](https://github.com/user-attachments/assets/c2412b96-2e28-4f7c-a169-d386298102ba)

-Log into Client-1 as Jane Admin and pull up Command Prompt, type in gpupdate /force, and press enter. You will get a successful message letting you know that the group policy has been updated. <br>

![15](https://github.com/user-attachments/assets/affcff2b-ee3c-436a-a2d8-ccdcb9456ba2)
 
- Logout of Clinet-1 as Jane admin so we can attempt login failures for dog.fomi to trigger account lockout. 
- Attempt logging into Client-1 as dog.fomi with the wrong password at least 6 times until account lockout. <br>

![16](https://github.com/user-attachments/assets/1351a6e5-4cf6-409c-ae5d-a3de0f517c7e)

- Now go into DC-1 as Jane Admin, Open Active Directory, and locate the user dog.fomi in my case. Double-click on the user to open properties, go to the account, and check the box next to Unlock account. > Apply > Okay. 
- Attempt to log in as dog.fomi and gain access to Client-1. 
We have now successfully reset a password as admin for a user who locked themselves out due to too many wrong password attempts. 

From this Active Directory, depending on the needs of your organization, we can disable an account. For example, someone getting fired, etc. You may need to disable an account immediately. 

- After logging into Client-1 as Jane Admin, open Command Prompt, type gpupdate /force. You will get a confirmation of success. <br> 

[14-edit]

<h3>Step _: Check Event Logs</h3>

To see the logon failure logs, we will need to look for them on the Client-1 computer where they occurred. 
- Press Start Windows Key, type eventvwr.msc, right-click, and open it as Admin. A normal user like dog.fomi would not be able to view the events without admin access. We will need to open the Event Viewer as an admin using Jane's admin credentials. 
- Once in the Event Viewer, under Windows Logs, click Security. Right-click on Security and click Find... > Type your user's name, mine is dog.fomi. Continue to click Find Next until you get to entries listed under Task Category that show continued failed login attempts. <br>

![17](https://github.com/user-attachments/assets/480ead83-86de-4195-a9df-06d136e69534)

This completes the project of setting up an active directory and executing functions such as initiating account activation after password attempt failures lock the user out. 

<h3>The End, Cleaning Up</h3>

This was a very long project, and you may want to experiment with many more things that can be done with Active Directory. 
- If you are done, as always, we will clean up. 
- If you are just done for now, make sure to pause both of our virtual machines from Azure and confirm they have been stopped successfully. This will save some of your resources. 
- If you are permanently done with these resources, delete the resource group which will remove DC-1 and Clinet-1 VMs with it, and wait for a notification of successful completion. Afterward, you can navigate to resource groups and virtual machines to ensure our resources were successfully deleted.<br>

To see other projects: https://github.com/brandenoz. 
