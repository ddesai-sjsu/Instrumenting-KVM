# Instrumenting-KVM

This is to add counters into the KVM that track the following information:
• Total number of exits (for each type of exit KVM enables)
• Max/Min/Average number of CPU cycles for each exit type
• Total amount of cycles spent processing all exits

The following procedure describes the steps followed to develop and test the kernel module:
1)	Install packages necessary to compile the linux kernel from source:
>> sudo apt­get install git build­essential kernel­package fakeroot libncurses5­dev libssl­dev ccache

2)	Clone the kernel sources from the master linux git repository:
>> git clone https://github.com/torvalds/linux.git

This clones the linux kernel sources to a directory named "linux".

3)	Change to the cloned directory:
>> cd linux

4)	Generate the kernel configuration file ".config" using the following command. If this command fails with any missing packages, install them using sudo apt-get install <package name>
>> make menuconfig
  
5)	Compile and install the kernel and its modules and also copy the kernel and the .config file to the /boot folder and to generate the system.map file using the following commands:
To know the number of processing units, in my case it was 1 core.
>> nproc
 Specify the number of cores in -j option in the following command to speed up the process.
>> sudo make -j 1 && sudo make modules_install -j 1 && sudo make install -j 1

6)	Run the following command to check the kernel version
>> uname ­r

7)	Reboot and select the newly built kernel in the boot loader config (if not already).

8)	Verify that we are running the newly built kernel using the following command:
>> uname ­r

For example, in the VM used for this assignment the output of "uname -r" before and after installing the new kernel are shown below:

Before reboot:
>> uname ­r
4.13.0.36-generic

After reboot:
>> uname ­r 
4.16.0+

Note: Once the above kernel is running, testing any new changes made to the KVM sources would just require rebuilding (and re-inserting) the kvm and kvm-intel modules, which is accomplished using the following command:

>> sudo make modules SUBDIRS=arch/x86/kvm/

### Module development
The changes to KVM, necessary to implement the assignment functionality required modification to the kvm_emulate_cpuid in the following file:
arch/x86/kvm/vmx.c

9)	Build only the kvm modules using the following command inside the linux directory:
>> sudo make modules SUBDIRS=arch/x86/kvm/

10)	Load and unload the kvm kernel module (kvm.ko) and kvm-intel module (kvm-intel.ko) using the following commands:
>> sudo rmmod arch/x86/kvm/kvm-intel.ko
>> sudo rmmod arch/x86/kvm/kvm.ko

>> sudo insmod arch/x86/kvm/kvm.ko
>> sudo insmod arch/x86/kvm/kvm-intel.ko

Now to test this change to CPUID.c create a VM (let’s call it VM-1).

11)	Use the following command to install KVM and supporting packages.
>> sudo apt-get install qemu-kvm libvirt-bin bridge-utils virt-manager

12)	Verify KVM Installation using the following command. You should see an empty list of virtual machines. This indicates that everything is working correctly.
>> virsh -c qemu:///system list

13)	Install Virt-Manager
>> sudo apt-get install virt-manager

14)	Create a test VM and install Ubuntu.

15)	Boot the test VM and check dmesg in the Host VM.
>> dmesg

### Sample of print output from dmesg

![1](https://user-images.githubusercontent.com/25673997/51422608-3b7a5d80-1b66-11e9-9ced-7ff97c330fbd.png)
