# Active-Directory-Splunk-Lab

<h2>Description</h2>
In this lab we will create be creating 4 different Virtual Machines (VMs). A Windows server with Active Directory, a Splunk server, a Windows target machine, and a Kali Linux instance. We will be setting up A Splunk server to monitor logs sent from our Windows server and target machine (enabled by Sysmon and Splunk Universal Forwarder), and later use Kali Linux to attempt an RDP (Remote Desktop Protocol) bruteforce on our Active Directory so we can analyze those logs. I will not be walking through the installation of the Windows or Kali machine, but will include a walkthrough of setting up and configuring the machines.

<h2>Environments & Tools used:</h2>

- <b>Splunk Enterprise Server</b> (https://www.splunk.com/en_us/download/splunk-enterprise.html)
- <b>Windows 10</b> (https://go.microsoft.com/fwlink/?LinkId=2265055)
- <b>Windows Server 2022</b> (https://info.microsoft.com/ww-landing-windows-server-2022.html)
- <b>Kali Linux</b> (https://www.kali.org/get-kali/#kali-virtual-machines)
- <b>Microsoft Active Directory</b>  ()
- <b>Oracle VirtualBox VM</b> (https://www.virtualbox.org/wiki/Downloads)
- <b>Sysmon</b> (https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)
- <b>Splunk Universal Forwarder</b> (https://www.splunk.com/en_us/download/universal-forwarder.html)

<h2>Pre-requsites:</h2>
I recommend at least 8gb of RAM to complete this lab. Any less and you likely will have problems running the machines simultaneously (often 3 are required to run at once.) Please set up the VMs in your virtualization software of choice. For this lab I am using VirtualBox. (Walkthrough on Splunk setup further in the lab.) Make sure the NAT type is set to NAT Networking, and allocate at least 25GB per machine (recommended 50GB). If you have less than 8GB of RAM, I recommend allocating 2GB of RAM for each machine. If you have >16GB, you may allocated more for a smoother experience. Let's begin!


*Note:*
*Please check the "Skip Unattended Installation" option for every machine in VirtualBox when initially setting up the machine as shown below.*

![3 example naming path](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/115de60e-964c-4137-8d55-ae6d95f297f9)


<h2>Visualizing this Lab</h2> 
In This Lab we will have 4 machines. First, our Splunk Server. It will be ingesting logs from our other machines and hosting them for us to view when we input it's target IP into our browser. Second, A Windows Server running AD (Active Directory) collecting and sending logs to the Splunk server via Sysmon & Splunk Universal Forwarder. Third, A Windows user machine connected to the Active Directory Server, also sending logs to Splunk via Sysmon and Splunk Universal Forwarder. Lastly, a Kali Linux instance we will be using to bruteforce an RDP logon on our target machine at the very end. I have pre-assigned IPs to each machine, and you may copy them to ensure everything runs smoothly.

![Active Directory Lab Diagram](https://github.com/TChungSEC/Active-Directory-Splunk-Lab/assets/164605938/62aeda4d-0085-44bf-89de-f93a2061a251)

<h2>Installing Splunk</h2> 
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

<h2>Install and Configure Sysmon and Splunk Universal Forwarder</h2> 

Now we need to set up Sysmon and Splunk Universal Forwader on our Windows 2022 Server and target machine. The process is exactly the same for both machines, so I will walk through how to do it on one machine, and you may replicate it on the other machine as well. 














<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
