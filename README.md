# PWF - Practical Windows Forensics

Goals: Perform a forensic analysis using the most open source tools on a Windows 10 system. Before the analysis you need prepare a target system and run an attack simulation script. We'll then go through data acquisition and evidence handling steps before setting up a forensic VM to investigate the system. 

Prerequisites:
  * Install VirtualBox (preferred) or VMWare hypervisor. See: https://bluecapesecurity.com/build-your-lab/virtualization/
  * Host system: 
      * Enough RAM for running 2 x Windows 10 VMs with 4GB RAM each (this does not have to be at the same time)
      * Enough disk storage for 2 x Windows 10 VMs using about 40 GB each. Additionally, you'll need roughly around twice the amount for handling disk images, memory images and additional artifacts. 160GB+ free space is ideal. 

## Prepare Target System
1) Install a free Windows 10 "victim / target" system that can be used for executing an attack and performing the investigation on
* Download: https://developer.microsoft.com/en-us/microsoft-edge/tools/vms/
* Import into VirtualBox and **take a snapshot before the first start**
* Start and log in to the VM. Credentials: "IEUser" and "Passw0rd!"
* **Disable all Defender settings**: Go to "Virus & threat protection settings" -> Manage settings -> Disable all the features
* Optional: Install Sysmon for detailed event logging.
    * Download Sysmon: https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon
    * Download Sysmon config script: https://github.com/SwiftOnSecurity/sysmon-config/blob/master/sysmonconfig-export.xml
    * Install Sysmon: Open cmd as administrator and run `sysmon64 -i sysmonconfig-export.xml`

2) Execute the attack script on the target system
* Download the script: PWF/AtomicRedTeam/ART-attack.ps1 onto the system
* Run PowerShell **as administrator!**, navigate to the script and execute it. 
    * Ensure that the system has internet access as it will download AtomicRedTeam. Furthermore, press [Y] Yes if PowerShell asks for installing additional features.
    * Verify that the powershell logs show successful executions of atomics. (If unsuccessful shut down the VM, revert to the previous snapshot and implement fixes before running the script again.)
    * Do **not close any windows or processes** and proceed to the next step!

## Data Acquisition 
3) Take an image of the VM memory
* In your hypervisor suspend or pause the VM

*VMWare memory acquisition*
  - Open the *.vmwarevm* directory of the VM in a terminal
  - Copy the .vmem file to a location that can be used for analysis later

*VirtualBox memory acquisition*
* Open your terminal (Mac/Linux) or cmd (Windows) to run *vboxmanage* (in Windows it is located under C:\Program Files\Oracle\VirtualBox)
* Identify the VM's UUID: `vboxmanage list vms`
* Create a snapshot of the VM's memory: `vboxmanage debugvm <VM_UUID> dumpvmcore --filename win10-mem.raw`
  
4) Take an image of the VM disk
* Unpause / Resume the VM
* Shutdown the Windows VM (this ensures updates from memory are written to the  system)

*VMWare disk image acquisition* 
* Locate the VMDK split files in the VM's directory. These are all the files ending with *.vmdk*.
    * Depending on the number of snapshots there could be several versions of VMDK file sequences. In that case the sequence with the highest number in the name will be the one with the latest status e.g. as in "Virtual Disk-XXX.vmdk"
* Copy all the split files of the latest sequence "Virtual Disk-xxx.vmdk" to "Virtual Disk-xxx-s0016.vmdk". 
  
*VirtualBox disk image acquisition*
* Open terminal or cmd
* Identify the VM's UUID: `vboxmanage list vms`
* Identify the VM's disk UUID: `vboxmanage showvminfo <VM_UUID>` Note the UUID of the disk in row *IDE Controller*
* Export the disk using the disk UUID into RAW format: `vboxmanage clonemedium disk <disk_UUID> --format raw win10-disk.raw`
  
5) Validate integrity of memory and disk images by creating SHA1 hashes and saving them in a text file along with the images.
  
*Windows*: Open PowerShell and navigate to the folder. Obtain hashes by executing: `Get-FileHash -Algorithm SHA1 <file>`
 
*Mac/Linux*: Open terminal and navigate to the folder. Obtain hashes by executing: `shasum <file>`
  
## Forensic analysis
6) Set up a forensic VM as outlined in the following link: https://bluecapesecurity.com/build-your-forensic-workstation/
* Install the following tools: 
    * Ubuntu Windows subsystem for Linux, Volatility
    * Arsenal Image Mounter, FTK Imager, Eric Zimmerman Tools, RegRipper, Notepad++
  
Take snapshots !
  
to be continued...
  
  
  
Copyright © 2022 BlueCapeSecurity
www.bluecapesecurity.com
  
