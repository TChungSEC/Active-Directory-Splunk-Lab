# Active-Directory-Splunk-Lab

<h2>Description</h2>
In this lab we will create be creating 4 different Virtual Machines (VMs). A Windows server with Active Directory, a Splunk server, a Windows target machine, and a Kali Linux instance. We will be setting up A Splunk server to monitor logs sent from our Windows server and target machine (enabled by Sysmon and Splunk Universal Forwarder), and later use Kali Linux to attempt an RDP (Remote Desktop Protocol) bruteforce on our Active Directory so we can analyze those logs. I will not be walking through the installation of the Windows or Kali machine, but will include a walkthrough of setting up and configuring the machines.

<h2>Environments & Tools used:</h2>

- <b>Splunk</b> (https://www.splunk.com/en_us/download/splunk-enterprise.html)
- <b>Windows 10</b> (https://go.microsoft.com/fwlink/?LinkId=2265055)
- <b>Windows Server 2022</b> (https://info.microsoft.com/ww-landing-windows-server-2022.html)
- <b>Kali Linux</b> (https://www.kali.org/get-kali/#kali-virtual-machines)
- <b>Oracle VirtualBox VM</b> (https://www.virtualbox.org/wiki/Downloads)
- <b>Sysmon</b> (https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)
- <b>Splunk Universal Forwarder</b> (https://www.splunk.com/en_us/download/universal-forwarder.html)
- <b>Microsoft Active Directory</b>

<h2>Pre-requsites:</h2>
I recommend at least 8gb of RAM to complete this lab. Any less and you likely will have problems running the machines simultaneously (often 3 are required to run at once.) Please set up the VMs in your virtualization software of choice. For this lab I am using VirtualBox. (Walkthrough on Splunk setup further in the lab.) Make sure the NAT type is set to NAT Networking, and allocate at least 25GB per machine (recommended 50GB). If you have less than 8GB of RAM, I recommend allocating 2GB of RAM for each machine. If you have >16GB, you may allocated more for a smoother experience. Let's begin!


*Note:*
*Please check the "Skip Unattended Installation" option for every machine in VirtualBox when initially setting up the machine as shown below.*

![3 example naming path](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/115de60e-964c-4137-8d55-ae6d95f297f9)

<h2>Setting up NAT Networking</h2> 
After installing all 4 VMs, refer back to this guide for setting up NAT networking on your machines.

In Virtualbox, navigate to Tool > Network.

![21 set network to NATTY](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/c6e23a5e-b120-4720-8535-b1b718f98e49)

From here navigate to the NAT Network tab, and click "Create". At the bottom name it to whatever you want. In the IP section, put 192.168.10.0/24. Enable DHCP and select "Apply".

![22 SETUP NATTY](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/e6db9b09-d768-4170-a503-f5a5c15ced14)

Now for *all* of your VMs you're using for this lab, select the VM, navigate to "Settings", select "Network" on the left hand side, and change the "Attached to" setting from "NAT" to "NAT Network".

![23 SETUP NATTY](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/deae55e7-8acb-47f2-938e-58bd021c7b5e)

<h2>Visualizing this Lab</h2> 
In This Lab we will have 4 machines. First, our Splunk Server. It will be ingesting logs from our other machines and hosting them for us to view when we input it's target IP into our browser. Second, A Windows Server running AD (Active Directory) collecting and sending logs to the Splunk server via Sysmon & Splunk Universal Forwarder. Third, A Windows user machine connected to the Active Directory Server, also sending logs to Splunk via Sysmon and Splunk Universal Forwarder. Lastly, a Kali Linux instance we will be using to bruteforce an RDP logon on our target machine at the very end. I have pre-assigned IPs to each machine, and you may copy them to ensure everything runs smoothly.

![Active Directory Lab Diagram](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/62aeda4d-0085-44bf-89de-f93a2061a251)

<h2>Installing and Configuring Splunk</h2> 
At this point in the lab, it is assumed you have already downloaded and setup the Windows target machine, Windows Server, and Kali in your virtualization software of choice already. If you have not done that yet, please do so with the settings I've specified in the pre-requisites section. After pre-configuring up Splunk and initializing it for the first time, you will met with this screen.

![7 Ubuntu install](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/44b57622-66ff-4581-9959-ed60583f6d3d)

Select "Try or Install Ubuntu Server" and press enter.

Next, select your language. In this lab we will be using English. 

![8 unbuntu install pt2](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/90ff9b02-1c15-4a13-8946-7923703913d1)


Continue pressing enter. You might be met with this screen. Go down and select "Continue".

![9 unbuvnti install pt2](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/c22da2e7-306c-4c19-bf20-df0cad6e3768)

Next you'll be met with the "Guided Storage Configuaration" Screen. Just press down and select "Done".

![9 unbuntu instlal pt 3](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/af8da48c-ced7-4283-9b98-097a4dea56ff)

Next you may get this "Confirm Destructive Action" Screen. Just select "Continue".

![9 unbuvnti install pt4](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/7e812a71-14d3-4b66-b1ec-99e356cca3a9)

Now we get to setup our credentials. *Please remember your username and password.*

![10 unbuntu install pt4](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/d5006e31-9f6d-4f9e-9f64-a8ffa60db669)

Continue through the the Unbunto pro screen, we don't need it. 

On the OpenSSH page, it's up to you if you want it. It is out of the scope of this lab but you may use it if you'd like later. In this lab I have opted out. 

Next, you'll be met with this page. Again, just scroll down and select "Continue".

![11 unbuntu install pt 5](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/50817739-a486-40c2-bb2c-121a531099dd)

Now Ubuntu will start installing. You will know when it's done installing when you see the "Reboot Now" option. Select it.

![13 ubuntu install finished](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/7a9c5918-643d-4d01-b100-2f91c86e0dcc)

After selecting "Reboot Now", you will get an error saying "Failed unmounting /cdrom". Don't worry, just press enter.

![14 ubuntu install error issokman](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/78c8176f-4c95-4635-a735-2cbf78c7967e)

After rebooting you will be met with the logon screen. Congratulations on installing Splunk! Login with the credentials you set earlier in the installation process.

![16 ubuntu install login](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/2fed2e31-8d4b-447e-893a-73a64149985a)

Now it's time to update and upgrade all our repositories. Use the command *sudo apt-get update && sudo apt-get upgrade -y* to do that. It will ask for your password again.

![19 correcy syntax](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/9bc2cf31-02df-4cfd-82dd-d60323cd5ce0)

After finishing you will be met with this screen. Go ahead and press enter and our server should be ready to go.

![20 upgrade n update screen](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/9cbc145b-7154-4423-9f2e-da3b6b619257)

Now that Splunk is installed and operational, refer to the "*Setting up NAT Networking*" of this guide above to enable NAT Networking on all your machines.

Back in Splunk, we need to set our IP to match our diagram (192.168.10.10). You can use the command *ip a* to see if you got lucky and it matches, but chances are it doesn't. So we need to configure our Splunk server to have that IP. Use the following command to do so.

![24 splunk config](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/9b73fc93-f89d-4689-9c4a-291437597e2d)

The dchp setting should be set to *true*, so remove that and type *no*

Press enter, and tab 3 times, then type *addresses:*. Press Space once, and in square brackets, type in our desired address: *[192.168.10.10/24]*

Press enter, and tab 3 times again. Type *nameservers:* and press space once.

Press enter again, and this time press tab 5 times. Type in *addresses: [8.8.8.8]*

Enter again, tab 3 times, and type in *routes:* press space once, and enter.

Tab 5 times, and type *- to: default*

Press enter again, and tab 6 times. Type *via: 192.168.10.1*

It should look like this:

![25 splunk configga](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/70793980-23fe-46ff-b55d-f645052494c9)

Now we want to save all this configuration by holding "Ctrl" + "x". Press "y" to confirm, and then "Enter". Done.

Now type *sudo netplan apply* to apply the new settings.

![26 apply](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/a1406109-91b0-4384-918d-6096c95dc46d)

If you type out *ip a* again it should show the correct IP address. You can ping google.com to see if you have a connection. If you failed to connect, please recheck your formatting on the network settings.

After you are configured, you can go ahead and download Splunk Enterprise on your host machine- Not your VMs. We want the Linux .deb file, located here: (https://www.splunk.com/en_us/download/splunk-enterprise.html?locale=en_us)

After downloading and saving it, continue ahead. We'll use it later.

Back in our Splunk Virtual Machine, we need to install the guest addons for Virtualbox. To do that, enter the following syntax:

![28 get VB](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/963f68f8-4e62-4961-81a6-de3d3a8ef26b)

It may take some time to install. After finishing, reboot and our package should be installed. 

We still need one more addon, so repeat the process above but install "guest-utils"

![31 guest-utils, pair with vbox install previous](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/9f05be2f-58a6-47bb-b82e-d253800e4f9d)

After installing and rebooting, continue with the next steps.

In the VB menu of your Splunk instance, select "Devices" > "Shared Folders" > "Shared Folder Settings..." Select the new folder icon.

![29 new folder](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/a450ec21-2866-407a-9e5c-7b25915bddc1)

We want to add the folder where we just downloaded our Splunk Enterprise installer to. Check all the boxes *(Read-only, Auto-mount, Make permanent)* and click "Ok".

![30 Add Share](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/f3371d1d-d1ee-491e-8bf1-e55dba1aa00a)

Now lets reboot our machine by typing *sudo reboot*.

Now it's time to add our user to the Vbox sf group. Type *sudo adduser yourusernamehere vboxsf*. It will prompt you for your password. Enter it, and now your user should be added to the group.

![32 adduser vbox](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/7d405add-dc5b-4c54-a32b-cf1daff7b3fd)

Next, make a directory called "share". Type *mkdir share* to create it.

Now we want to mount our shared folder to our directory called "share" to do that, type the following. Make sure to replace the name of the folder with whatever you named yours.

![33 link share foolder](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/5a3032af-ca11-41fb-a9b2-7e4c1188d068)

Confirm that we were successful by typing *ls -la*.

![34 it exists](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/d7e105b5-53b9-48b5-82c1-a24e7d1b05be)

After confirming our directory was made, we will change directories into "share". Type *cd share/*.

Now type ls -la to see all the files listed in the directory, including our splunk installer from our host machine.

![35 cd baby](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/ad62c7e9-acd6-4fc7-ae5f-fb9902c002b9)

Now we will install Splunk Enterprise from the share folder by typing the following:

![36 install splunk](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/0c04f406-0e1e-4d2b-9e57-9db8baa9bb7f)

After it completes we can change directory to where Splunk is located. This will be under */opt/splunk*. So cd there.

Let's change to the user splunk. To do this, type: *sudo -u splunk bash*. 

![37 become the SPLUNK](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/054d3b9b-fd66-4b4b-88e1-9269eb62a73f)

Now change directory to bin. Type *cd bin*. This is where all the binaries Splunk can use are located. We are going to use *./splunk*. Type *./splunk start* to begin the installer.

We'll get the licenses and agreements page, hit "q" then "y" to accept, followed by the administrative username and password. 

After finishing, lets input one more additional command to make sure Splunk starts up every time we boot and reboot our virtual machine.

Type *exit* to exit out of the user splunk and back to the administrative user. Then, *cd bin*.

After that, input the following:

![38 start splunk errytime](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/7628a225-a782-417f-bc5f-73d1aa227104)

Now, anytime the virtual machine starts it will run as the user "splunk". Congratulations, we are done configuring our Splunk machine!

<h2>Installing and configuring Sysmon and Splunk Universal Forwarder on Windows Machines</h2> 

The process for installing and configuring both Sysmon and Splunk Universal Forwarder is nearly identical for both machines. This walkthrough shows how to do it on the Target PC, but you can emulate the steps again to configure for the Windows Server as well.

Let's begin configuring our Target Machine. Firstly, let's change the hostname to Target-PC. In the windows search menu, search for "PC".

Select properties from the "This PC" app, and then select "Rename".

![39 renamepc](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/64a3f8e2-019f-4ae0-965f-e36497d0e21d)

Let's name it *"Target-PC"*. Confirm and restart the VM.

Now, let's change our IP so it doesn't interfere with any of our other machines.

Right click the Network icon in the lower righthand screen, and select "Open Network & Internet settings".

Scroll down to "Change adapter options". Right click the adapter, then select "Properties", and double click Internet Protocol version 4 (TCP /IPv4).

We will set a static IP of *192.168.10.100*. You can copy my settings below.

![39 ip fix](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/6756fefa-1165-4da9-8e8c-b85d3c69b18c)

Now we should have a static ip set. You can double check in the command prompt by typing *"ipconfig"*.

Next, let's check on our Splunk server. 

To do this, in a web browser navigate to *192.168.10.10:8000*

It should bring up a login page.

![40 splunk connected](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/1e4bd51b-7ab6-4609-b845-13b72a028e54)

Now, let's install Splunk Universal Forwarder. Navigate to (https://www.splunk.com/en_us/download/universal-forwarder.html) and download the correct version for you Windows architecture. In my case, it is the 64-bit version.

After downloading SUF, head to the folder you downloaded it to and run the .msi file.

Check the box to accept the licensing agreement, and also make sure the "An on-premises Splunk Enterprise instance" is checked, not the cloud option.

On the next page designate the username *"admin"* and leave the "Generate random password" on and click next. 

On the Deployment server page, leave it empty and click next.

On the Receiving Indexer page, we want to input our Splunk server's information. In our case it is *192.168.10.10*. The default port is 9997, so leave it as such and select "Next", and "Install".

![41 splunk UF setting install](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/25542a44-cf10-4807-9fe6-9d319058c24a)

After the SUF is installed, let's install Sysmon. Navigate to (https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon) and select "Download Sysmon".

We need a custom Sysmon configuration so get it here: (https://github.com/olafhartong/sysmon-modular).

Scroll down and select *"sysmonconfig.xml"*

![42 sysmon config](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/9935a4be-952a-4f13-8281-447e879bca00)

Select "Raw", then right click and "Save as" in your downloads directory. 

Now navigate to your downloads directory and you should see the Sysmon folder you downloaded earlier. Right click it and "Extract all".

Next open Powershell and *cd* to the file path of your extracted Sysmon folder. Once in the folder, run sysmon.exe using the configuration we just downloaded by inputting the following:

![43 install sysmon](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/001b621c-e831-4920-91e4-74f27a85c8b7)

Press enter, click agree, and Sysmon should install. You can close out Powershell after this.

*Now is the most important part.* We need to instruct our Splunk Forwarder on what we want to send over to our Splunk server.

Navigate to *C:/Program files/SplunkUniversalForwarder/etc/system/local*.

Now run Notepad as administrator, and paste in the following:

[WinEventLog://Application]

index = endpoint

disabled = false

[WinEventLog://Security]

index = endpoint

disabled = false

[WinEventLog://System]

index = endpoint

disabled = false

[WinEventLog://Microsoft-Windows-Sysmon/Operational]

index = endpoint

disabled = false

renderXml = true

source = XmlWinEventLog:Microsoft-Windows-Sysmon/Operational

Save this file to *C:/Program files/SplunkUniversalForwarder/etc/system/local* as inputs.conf

After changing our inputs file, we must always restart the SUF service. Search for "services" and run as administrator. Find the SplunkForwarder service and scroll to the right. Under the "Log on as" column, you will see it defaulted as NT SERVICE. We need to change it to Local System Account. To change it, double-click the service, and change the tab to "Log on", and check the "Local System account" setting. You will get a notice to restart the service, which we'll do next.

![44 splunk service](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/ab0f50bd-a0d0-4e63-93b1-dddf013a3682)

Right click the SplunkForwarder service and selecrt "Restart".

Now we both Sysmon and SUF installed, along with the updated inputs.conf file. Now we can finalize our Splunk server configuration.

Head back to the Splunk web portal at *192.168.10.10:8000* and login using the credentials we created during the splunk install on our server.

![40 splunk connected](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/4282f87a-b225-4acf-b461-0a81f13738f8)

On the launch screen, select "Settings" at the top and then from the drop-down menu select "Indexes".

![44 indexes](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/2ccd85bc-5919-429f-adef-43d54b7e893b)

If you remember from our configuration file, it is sending our logs to an index called endpoint. It doesn't exist yet so we need to create it now.

Select "New Index".

Type in "endpoint" as the name and then save. 

![44 endpoint](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/aabb7b14-3605-42d3-9be3-b8c93b3461a1)

Next we need to enable our Splunk server to receive the data. 

Navigate to "Settings" > "Forwarding and receiving".

On this page under "Receive Data" select "Configure receiving".

![44 receive data](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/62a06b44-1999-45e4-9a47-92bec1345750)

In the upper right hand corner select "New receiving port".

In the "Listen on this port" field input *9997*, and click "Save".

If everything is configured correctly, we should now be receiving data to our endpoint index.

To verify, navigate to "Apps" in the top left corner, then from the drop down menu select "Search & Reporting".

In the search box you can search for *index=endpoint*. If events are logged and your machine (Target-PC) is showing up in the "hosts" section, everything is configured successfully.

![44 index endpoint search](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/2d38573a-e22a-4b55-a34d-e4ae41446b7a)

Congratulations! You have successfully installed and configured Sysmon and Splunk Universal Forwarder on your target machine. Now you can follow the same steps to install Sysmon and SUF on your Windows Server machine.

<h2>Install and Configure Active Directory on Windows Server</h2> 

Now it's time to get Active Directory up and running on our Windows Server. After that we will promote it to the Domain Controller, and then finally configure our target machine to join our domain. Let's get started!

Start by booting up the Windows 2022 Server if you haven't already. Once logged in, open the Server Mananger.

Here, navigate to the top right and select "Manage" > "Add Roles & Features". Keep clicking "Next" until you reach the "Server Roles" section. Here, select "Active Directory Domain Services" and then "Add Features". 

![45 install ADDS](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/69d8bfca-d2fb-4e01-9108-0b404feb5595)

Continue clicking "Next" until you get to the "Install" option. It should take a few minutes to install, but when it finishes you can select "Close".

Now you can click on the flag icon next to "Manage". Click "Promote this server to a domain controller".

![46 promote 2 contorlla](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/dc174073-5292-47ce-9c10-398a636a70fa)

On this page, select "Add a new forest" and set the Root Domain Name as anything you'd like, as long as it has a "." in the middle. You must include the "." because your domain name must have a top level domain. I have set the name to *test.local*. After choosing a name select "Next".

![47 root domain](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/c9fcae04-dd41-4bfc-b566-19e44de99197)

In the "Domain Controll Options" page you must put in a password. Remember it.

Keep selecting "Next" until you are able to install. After finishing, your server should automatically restart. You'll notice after unlocking this time, in the bottom left hand corner you'll see your recently created domain followed by a "/". This indicates that we have successfully installed ADDS and promoted our server to a domain controller. Congrats! You got Active Directory up and running on your Windows Server. Now log in.

![48 AD SUCCESS](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/4454f35a-1867-48a0-b21f-0165df69dade)


<h2>Creating New Users</h2> 
After logging in, in the Server Manager navigate to "Tools" in the top right corner, and then select "Active Directory Users and Computers" from the drop-down menu. This is where we can create objects such as users, groups, and more.

![49 DOMAIN EXPANSION](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/d630e286-f339-4cd7-ba5a-cec2fa26dd84)

Let's create a new Organizational Unit for our domain to mimic a more life-like scenario, where departments are usually separated. 

Right click your domain, and in the drop-down menu navigate to "New" > "Organizational Unit".

Name it "IT" and click "OK". Now a new Organizational Unit folder called "IT" should appear under our domain.

![50 New folda](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/4c6cbf73-f120-436c-abb5-6eb67ecff7af)

Now right click the folder and from the drop-down menu, navigate to "New" > "User".

We'll name him "Tao Chung", and make his username "tchung", then select "Next". Set the password as something you will remember, because we'll need to add it to our brute force dictionary later on. Also uncheck "User must change password at next logon", because this is just a lab environment and it's unnecessary.

![51 NEw user](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/81ab6ce4-39f3-4aa2-a7be-7109899354b0)

We can see our new user has been created.

![52 new user added](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/1dd82ec3-e64e-40d3-8e03-85859004cad9)

Now Let's create another Organizational Unit called "HR". Here we will add a user named "Jenny Smith" with the username "jsmith".

![53 New user new domain](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/9c124ed1-098a-4b4f-857a-b7ac7e9fb429)

<h2>Joining Windows Machine to New Domain</h2>
Now that Active Directory is set up and our Server is now the domain controller, we will now join our Windows machine to our newly created domain *test.local*, and authenticate using Jenny Smith's account.

On our Windows 10 machine, search up "PC" then click on "Properties". Scroll down and select "Advanced system settings". Select the "Computer Name" tab, and then select "Change".

Select the Domain option, and enter your newly created domain *test.local*

![54 new doman pc](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/b6d4329e-0923-44c2-b8f9-18702cfcb41e)

You will probably get an error message saying the domain could not be contacted. This is because our target machine does not know how to resolve *test.local* 

![55 error](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/e356b0f7-fd02-46fb-9a08-6a8963708fed)

We can fix this by right clicking our network adapter, and selecting "Open Network & Internet settings". From here, select "Change adapter options". Right click your adapter, and select "Properties", then double-click "Internet Protocol Version 4 (TCP/ IPv4)". Our DNS server should be pointing to *8.8.8.8*. We need to change it to point at the domain controller *(192.168.10.7)*. Keep everything the same and just change the preferred DNS server to our domain controller. Select "OK" and close out of the window.

![56 dns server fixed](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/550c2ecc-6b4f-448d-adab-c735070b0e82)

Now head back to our domain options where we were previously denied, and we should get a prompt to login. Enter our domain's administrator credentials and select "OK". A window should appear that says "Welcome to the *test.local* domain. Select "OK", and "OK" again, and you'll be prompted to restart the computer. Select "Restart Now".

![57 success](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/5c84b2e2-6afb-4e2e-ac3e-987c4ddb611e)

Once you're at the logon screen, select "Other User" in the bottom lefthand corner. We're going to log on as Jenny Smith.

![58 other user](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/75f4a774-c192-4691-badc-60b6b9e9312c)

Congrats, you should be logged in as user "jsmith" now. Your target machine is now configured and joined to the domain. You now have a fully functional Active Directory server lab to play around with. I recommend snapshotting your VM here incase you break anything while learning so you can restore from a fresh state.

![59 grats!](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/f882d2fe-e4e8-4c14-ab0a-abcc02d42810)

<h2>Using Kali Linux to Conduct a Brute Force Attack</h2>
































































































































<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
