# Initial Setup

Every environment is different, but for this project I am using **two Raspberry Pi 5 devices**, both running a **fresh installation of Ubuntu Server LTS**. Each Raspberry Pi uses 
a **microSD card** as its primary storage, so the first step is flashing the operating system onto the microSD cards.

---

## Flashing the microSD Card

Using a **microSD card adapter**, I connected the microSD card to my laptop, which includes a built-in SD card reader.

To flash the operating system, I used the **Raspberry Pi Imager** application. This tool greatly simplifies the process of preparing a bootable microSD card for Raspberry Pi devices.

![image.png](/Screenshots/Initial_setup/01-initial_setup.png)

First, select the appropriate **Raspberry Pi model**.

![image.png](/Screenshots/Initial_setup/02-initial_setup.png)

Next, choose the operating system. For this project, I selected:

**Ubuntu Server 24.04.3 LTS**

LTS stands for **Long-Term Support**, which provides extended security updates and stability—ideal for server environments.

When prompted, select the target storage device (the microSD card). In most cases, there should only be one available option.

![image.png](/Screenshots/Initial_setup/03-initial_setup.png)

---

## Customization Settings

During the customization stage, assign a **hostname** to the Raspberry Pi. This hostname will be used to identify the device on the network. Choose a clear and descriptive name, especially when working with multiple nodes.

![image.png](/Screenshots/Initial_setup/04-initial_setup.png)

Next, create a **username and password** for the administrative user.

In the networking section, enter your **Wi-Fi SSID and password** if you plan to use wireless networking. This allows the Raspberry Pi to automatically connect to the network on first boot.  
If you plan to use Ethernet exclusively, this step can be skipped.

![image.png](/Screenshots/Initial_setup/05-initial_setup.png)

Ensure that **SSH (Secure Shell)** is enabled. SSH allows remote access to the Raspberry Pi through a terminal and is essential for headless server operation.

![image.png](/Screenshots/Initial_setup/06-initial_setup.png)

Before proceeding, review all selected options. When ready, click **“I understand, Erase and write”**.  
This process will overwrite all existing data on the microSD card. Once writing is complete, the application can be closed.

---

## Preparing Multiple Raspberry Pis

After flashing, insert the microSD card into the Raspberry Pi and power it on.  
Since this cluster consists of **two Raspberry Pi devices**, this entire process was repeated for each node.

This is the foundational concept of a **cluster**—multiple machines working together. Additional Raspberry Pis can be added later if resources allow. When working with multiple devices, it is important to assign **unique and easily identifiable hostnames** to each node.

Ensure that each Raspberry Pi receives **adequate power** using proper power adapters. Insufficient power can lead to unexpected shutdowns or service instability.

---

## Connecting via SSH

Once the Raspberry Pi is powered on and connected to the network (via Ethernet or Wi-Fi), you can connect to it remotely using SSH.

Before connecting, you must determine the device’s **IP address**. There are several ways to do this:
- Viewing connected devices through your router’s web interface
- Using a router management application
- Connecting a monitor and keyboard directly to the Raspberry Pi

In my case, I used the ASUS router management interface to locate the assigned IP address.

![image.png](/Screenshots/Initial_setup/07-initial_setup.png)

From a separate computer, open a terminal and run:

bash:
ssh <username>@<pi-ip-address>

In my case, the output looked slightly different because the Raspberry Pi had been previously installed with a different operating system. This results in an SSH key mismatch, which is expected after reinstallation.

To resolve this, remove the old SSH key entry:
ssh-keygen -R <pi-ip-address>

Then re-run the SSH initial command. 

![image](/Screenshots/Initial_setup/08-initial_setup.png)

when prompted to continue connecting, type "yes", then enter the user password.

![image_holder](/Screenshots/Initial_setup/09-initial_setup.png)

Once authenticated, you are successfully connect to the Raspberry PI. 

![image](/Screenshots/Initial_setup/10-initial_setup.png)

your terminal prompt should now display the Rasberry Pi hostname = which means you connected successfully. 
