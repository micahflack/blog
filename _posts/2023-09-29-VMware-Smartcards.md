---
layout: post
title: VMware Smartcard Support
---

If you have had diffuculty getting the VMware Horizon Client to share access to your smartcards on Linux, there are few reasons why that might be happening.

First off, we can check by referring to this knowledge base post from VMware for adding smartcard support to Ubuntu VMs - [Set Up Smart Card Redirection on an Ubuntu/Debian Virtual Machine](https://docs.vmware.com/en/VMware-Horizon/2306/linux-desktops-setup/GUID-FCF20C28-D384-4648-A20B-84144E119FED.html). The advice is the same for hosts. All you need to do is install these packages:

    sudo apt-get install -y pcscd pcsc-tools pkg-config libpam-pkcs11 opensc libengine-pkcs11-openssl libnss3-tools

And, you might need these ones as well if you have errors later:

    sudo apt-get install --reinstall pcscd libpcsclite1

Then, with your badge reader connected and the smart card inserted, verify access with these commands:

    sudo pcsc_scan
    sudo pkcs11_listcerts
    sudo pkcs11_inspect

And, that should be all you need for the host to use smartcards. If you still have issues getting the Horizon Client to share the connected cards, then you might need to remove and reinstall the software.

Start by removing the client:

    sudo apt remove vmware-view

Then, navigate back to the VMware downloads page for the Horizon Client and make sure you download the ".bundle" installation.

### TRYING TO USE THE .DEB PACKAGE WILL NOT WORK

Navigate to your downloads folder and make the bundle executable.

    cd ~/Downloads
    chmod +x VMware-Horizon-Client-2306-8.10.0-21964631.x64.bundle
    sudo ./VMware-Horizon-Client-2306-8.10.0-21964631.x64.bundle

This will run you through the setup and then eventually ask you which features should be enabled. Just click the "Scan" option instead, it will automatically check each feature installed correctly.

Once the installer finishes, open and login to the Horizon Client. Your smartcard should now be shared with the VDI.

If the client font looks unreadable, refer to my other post [Unreadble Fonts](https://micahflack.com/Linux-Fonts/)