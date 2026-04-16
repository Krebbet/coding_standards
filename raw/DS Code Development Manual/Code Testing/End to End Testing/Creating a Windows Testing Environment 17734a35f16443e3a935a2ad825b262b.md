# Creating a Windows Testing Environment

## Creating a Windows EC2 Instance

Create an EC2 instance, for this example I have selected the Windows 2019 Server base image. Another may be more appropriate depending on what the end user will be deploying on.

![](Creating%20a%20Windows%20Testing%20Environment/Untitled.png)

Next, select an ec2 instance type appropriate for the project.  If you need a GPU, make sure to select an instance with one!

![](Creating%20a%20Linux%20Testing%20Environment/Untitled%201.png)

Click 'Review and Launch', in the following screen select 'Edit Tags' and add a tag for the project you are testing

![](Creating%20a%20Linux%20Testing%20Environment/Untitled%202.png)

Then, click 'Review and Launch', then 'Launch' in the bottom right of the following screen. When prompted to select a keypair, ensure you have a local copy of the keypair.

![](Creating%20a%20Windows%20Testing%20Environment/Untitled%201.png)

## Connecting to Your Instance (Remmina)

Install [Remmina](https://remmina.org/)

On Ubuntu this is as easy as

```bash
sudo apt install remmina
```

[https://remmina.org/](https://remmina.org/)

Next, go to the AWS EC2 console, select your instance, click the 'Actions' button, and select connect. You should be greeted with a dialog box that looks like this.

![](Creating%20a%20Windows%20Testing%20Environment/Untitled%202.png)

Click 'Get Password', and select your local .PEM file associated with the keypair setup for this instance.

![](Creating%20a%20Windows%20Testing%20Environment/Untitled%203.png)

Click 'Decrypt Password', copy the administrator password displayed, and then download the remote desktop file

![](Creating%20a%20Windows%20Testing%20Environment/Untitled%204.png)

Now you can open the downloaded file with Remmina, accept the certificate when prompted.  Then paste the password into the password box, leave 'Domain' empty. You can let it remember the password if you want to as well.

![](Creating%20a%20Windows%20Testing%20Environment/Untitled%205.png)

Click ok and after 30 seconds or so the desktop should be setup. If you have a very small screen enable 'Dynamic Resolution Update' In remmina. There is a toggle button for this on the left-hand side of the UI for version 1.4.2. It should look like this.

![](Creating%20a%20Windows%20Testing%20Environment/Untitled%206.png)

## Disabling Internet Explorer Enhanced Security Configuration

By default, internet explorer will be able to do very little.  In order to download any files, or visit most websites you will need to disable internet explorer enhanced security.  The specific way to do this depends on the version of Windows Server you are using.  Good instructions for Windows Server 2019 are available [here](https://medium.com/tensult/disable-internet-explorer-enhanced-security-configuration-in-windows-server-2019-a9cf5528be65). You just need to open Server Manager, select Local, and select the option as shown.

![](Creating%20a%20Windows%20Testing%20Environment/Untitled%207.png)