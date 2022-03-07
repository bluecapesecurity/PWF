# PWF - Practical Windows Forensics

Prerequisites:
* Install VirtualBox (preferred) or VMWare hypervisors
* Enough RAM for running 2 x Windows 10 VMs with 4GB RAM each (this does not have to be at the same time)
* Enough disk storage for 2 x Windows 10 VMs with about 40 GB each. Additionally, you'll need roughly around twice the amount for handling disk images, memory images and additional artifacts. 160GB+ free space is ideal. 

## Prepare Target System

1) Install a Windows 10 "victim / target" system that can be used for executing an attack and performing the investigation
- Download: https://developer.microsoft.com/en-us/microsoft-edge/tools/vms/
- Import into VirtualBox and **take a snapshot before the first start**
- Start and log in to the VM. Credentials: "IEUser" and "Passw0rd!"


2) Execute the attack script on the target system

- Download the script: AtomicRedTeam/ART-attack.ps1 onto the system
- Run PowerShell **as administrator**
- Verify that the powershell logs show successful executions of atomics. (If unsuccessful shut down the VM, revert to the previous snapshot and implement fixes before running the script again.)
- Do not close any windows or processes and proceed to the next step!

## Data Acquisition 

3) Take a snapshot of the VM memory
- In your hypervisor suspend or pause the VM

*VMWare memory acquisition*
- Open the *.vmwarevm* directory of the VM in a terminal
- Copy the .vmem file to a location that can be used for analysis later

*VirtualBox memory acquisition*
- Open a console to run *vboxmanage* (in Windows it is located under C:\Program Files\Oracle\VirtualBox)
- Identify the VM's UUID: *vboxmanage list vms*
- Create a snapshot of the VM's memory: vboxmanage debugvm <UUID> dumpvmcore --filename win10.raw
  
4) Take a snapshot of the VM disk
- Unpause / Resume the VM
- Shutdown Windows (this makes sure updates from memory are written to the  system)

*VMWare disk image acquisition* 
- Locate VMDK split files in the directory
- Copy all .vmdk split files that belong to the *latest status* of the VM. This means the highest number after the file name "Virtual Disk-xxx". 
  A sequence of split files is e.g. "Virtual Disk.vmdk" to "Virtual Disk-s0016.vmdk". 
  
*VirtualBox disk image acquisition*
- Identify the VM's UUID: *vboxmanage list vms*
- Identify the VM's disk UUID: *vboxmanage showvminfo <VM_UUID>
-- Note the UUID of the disk in row *IDE Controller*
- Export the disk using the disk UUID into RAW format: vboxmanage clonemedium disk <disk_UUID> --format raw win10-disk.raw 
  
5) Validate integrity of memory and disk images by creating SHA1 hashes and saving them in a text file along with the images.
  
## Forensic analysis
  
6) Set up a forensic VM as outlined in the following link: https://bluecapesecurity.com/build-your-forensic-workstation/
  Install the following tools: 
- Ubuntu Windows subsystem for Linux, Volatility
- Arsenal Image Mounter, FTK Imager, Eric Zimmerman Tools, RegRipper, Notepad++
  
Take snapshots !
  
to be continued...
