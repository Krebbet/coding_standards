# Creating a Linux Testing Environment

## Creating a Linux EC2 Instance

Create an EC2 instance, for this example I have selected an Ubuntu 18.04 Server base image. Another may be more appropriate depending on what the end user will be deploying on.

![](Creating%20a%20Linux%20Testing%20Environment/Untitled.png)

Next, select an ec2 instance type appropriate for the project.  If you need a GPU, make sure to select an instance with one!

![](Creating%20a%20Linux%20Testing%20Environment/Untitled%201.png)

Click 'Review and Launch', in the following screen select 'Edit Tags' and add a tag for the project you are testing

![](Creating%20a%20Linux%20Testing%20Environment/Untitled%202.png)

Then, click 'Review and Launch', then 'Launch' in the bottom right of the following screen. When prompted to select a keypair, ensure you have a local copy of the keypair.

![](Creating%20a%20Linux%20Testing%20Environment/Untitled%203.png)

## Connecting to your Instance from Linux (SSH)

Identify the public IP for your instance in the ec2 console

![](Creating%20a%20Linux%20Testing%20Environment/Untitled%204.png)

Then, ensure your local .pem file has the correct permissions.  For my keypair this looks like the below.

```bash
chmod 0400 e2e_keypair.pem
```

Replace e2e_keypair.pem with your own .PEM file.

Then, connect to the ec2 instance with

```bash
ssh -i [Your Pem File Here] [IAM Username]@[EC2 IP Address]
```

The username depends on the AMI selected.  For the ubuntu AMI I selected above, the username is 'ubuntu'.  With that in mind, my command works out to

```bash
ssh -i ~/.ssh/e2e_keypair.pem ubuntu@54.174.209.187
```

After executing this command, you will find yourself at a terminal inside the ec2 instance.

## Connecting to your Instance from Linux with X11 Forwarding (SSH)

X11 forwarding allows some graphical applications ran on a remote machine to be rendered on a local machine.

This works in the same way as the above, but with the addition of a -X to the ssh command.

```bash
ssh -i [Your Pem File Here] -X [IAM Username]@[EC2 IP Address]
```

![](Creating%20a%20Linux%20Testing%20Environment/remote_gedit.gif)