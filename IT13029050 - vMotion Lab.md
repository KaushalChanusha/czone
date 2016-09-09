#How to do vMotion Migration in VMware#
#ESBPII  -  LAB 07 - Vmotion#
#Name - Gunarathne G.M.C.K. #
#ID No  - IT1329050#


----


#Introduction about vMotion#
VMware VMotion enables the live migration of running virtual machines from one physical server(ESXi host) to another with zero downtime(No service lost), continuous service availability, and complete transaction integrity. It is transparent to users.
This function done at real time without the user of the virtual machine even knowing they have been moved. V-Motion is the first step among many
VMware software solutions that are incorporated to make sure that downtime is kept to a minimum, which include Fault Tolerance, High Availability,
and Digital Resource Scheduler. This Technology can also used to improve the  hardware utilization.


##

#virtual Machine Requirements for vMotion Migration#
#### 01. Need a virtual Center ####
#### 02. Need physical servers with ESXi installed ####
#### 03. Need a Gigabit network cable to connect from one server to another directly. ####
#### 04. Connect a network cable to "Direct Attach" which means to connect directly from one server ####
#### 05. A virtual machine must not have a connection to a virtual device (Such as a CD-ROM or floppy driver) with a local image mounted. ####
#### 06.A Virtual machine must not have a connection to an internal vSwitch(vSwitch with zero up link adapters.) ####
#### 07. A Virtual machine must not have CPU affinity configured. ####
#### 08.CPU compatibility ####
#### 09.Access to the same physical networks. ####
#### 10.Visibility to all storage ####
#### 11. sufficient resources on the target host ####
#### 12.shared central mass storage####

##

#Compatibility requirements of vMotion#

There are some compatibility requirements that vMotion required for perform correctly.

what it's required,

- Each host must be correctly licensed.

- Each host must meet shared storage requirements.

- Each host must meet the networking requirements.

- Each compatible CPU must be from the same family.

If a virtual machine is successfully running an application on one host and you migrate it to another host without these capabilities the application
would most likely crash, possibly even the whole server would crash, hence why vMotion compatibility is required between hosts before you can
migrate a running virtual machine.

##

#CPU (Processor) Compatibility requirements of vMotion #

####Identify CPU characteristics####

One way to determine whether or not you have compatible CPUs is to use the vmware CPU identification utility which is downloadable from vmware. You can download that and so burn it onto a CD, pop that CD into each host one at a time and boot the host and you'll see a report as in following picture.

There are some different kinds of processors manufactured by different companies such as AMD and Microsoft. Those companies also developing different processors with different performances.The CPU compatibility problem is easy to explain. Imagine that a virtual machine is started on an ESX host with an AMD CPU and SSE3 functionality. Since VMware ESX is a virtualizer, the guest operating system sees all of the standard CPU functionality and can be adapted to the hardware with extra drivers to more effectively utilize multimedia functions.  

If this virtual machine is simply transferred to another host with a CPU that only supports SSE2, the guest operating system will still want to use the SSE3 functionality. This can cause problems or even a system crash. While these problems can sometimes be managed by so-called “CPU masking”, very large differences between CPUs remain unresolvable. Examples of large differences include switching from an AMD to an Intel CPU, or from a 64-bit to a 32-bit CPU. 

vMotion transfers the running architectural state of a virtual machine between underlying ESXi hosts. vMotion compatibility means that the processors of the destination host must be able to resume execution using the equivalent instructions where the processors of the source host were suspended. Processor clock speeds and cache sizes might vary, but processors must come from the same vendor class (Intel versus AMD) and the same processor family to be compatible for migration with vMotion.

Processor families are defined by the processor vendors. You can distinguish different processor versions within the same family by comparing the processors’ model, stepping level, and extended features.

Another Main requirements is memory. If we want to run couple of virtual machines  required atleast 12 or 16 GB RAM.

there are some methods that can satisfy CPU requirements,

- Procure servers with identical CPUs.
- Compatibility masking in the vSphere client.
- Enhanced vMotion Compatibility (EVC)
	- Automatically masks off CPU incompatibility
	- A feature of DRS clusters

##

#Advantages/Benefits of vMotion#

 • Main advantage is enables the live migration of running virtual machines from  one physical server to another with zero downtime

 • Include Fault Tolerance, High Availability, and Digital Resource Scheduler.

 • Automatically optimize and allocate entire pools of resources

By having all server and/or desktops virtualized we can move VM’s from one physical host to another, which is done rapidly over a high speed
network connection, the original host and destination host stay in sync until the transfer it complete leaving the user unaware of the move. This
allows network administrators to easily select resource pools to assign to the different VMs.

• Move VM’s from failing or underperforming priorities

If there looks like a server is about to fail or is reaching its capacity, administrators can manually move VMs to another physical host, this allows us
data center to be more dynamic in nature. Instead of having to upgrade hardware, we can move VM to another host to allow each VM to be more
flexible in nature. If 2 VM's are putting a physical host to capacity then we could move one to another server that isn't being used as much.

• Minimizes scheduled Downtime

90% of downtime is scheduled, before V-Motion administrators had to do server maintenance late at night in order to avoid disrupting users.
Having all the servers as virtual machines, we only have to move the VM to another physical host, creating zero downtime for the users and
allowing administrators to perform maintenance at any time. With DRS (Digital Resource Manager), all we have to do is put a server in
maintenance mode and V-Motion will automatically move all VM's to another server.

##

#Disadvantages of vMotion#

• Need to be aware of is the BIOS settings of these hosts need to Enable Hardware Virtualization and Execute Protection. 

• There is restriction of the EVC does not allow for migration with vMotion between Intel and AMD Processors

When weighing in the disadvantages, application performance springs first to mind. For instance, if SSL intensive applications are your thing, you
should avoid at all costs the inclusion of servers equipped with pre-Westmere generation processors.

##

#Pros and Cons of vMotion#

##Pros##

• Dynamic allocation services of the software allow you to allocate resources and memory while the processes are running.

• Security service of the software is flexible and allows you to implement user defined policy enforcement.

• The software supports all major operating systems as well as wide range of hardware.

• V-Motion allows to precisely identify the optimal placement for virtual machine.

• Virtual machines can be optimized within resource pools automatically.

# Cons #
• There is no module for performance management on the software.

• Unlike its competitors, the software does not offer any reporting capabilities.

• Configuration features of the software do not include auto recovery, configuration history, and NIC teaming capabilities.

• There are no performance management features of adaptive analysis, memory compression, and continuous resource allocation on the software.

##


#vMotion Migration working procedure#

01) The entire state of a virtual machine is encapsulated by a set of files stored on shared storage. VMware’s clustered Virtual Machine File
System (VMFS) allows multiple installations of ESX Server to access the same virtual machine files concurrently.

02) The active memory and precise execution state of the virtual machine is rapidly transferred over a high speed network. This allows the
virtual machine to instantaneously switch from running on the source ESX Server to the destination ESX Server. V-Motion keeps the transfer period
imperceptible to users by keeping track of on-going memory transactions in a bitmap. Once the entire memory and system state has been copied
over to the target ESX Server, V-Motion suspends the source virtual machine, copies the bitmap to the target ESX Server, and resumes the virtual
machine on the target ESX Server. This entire process takes less than two seconds on a Gigabit Ethernet network.

03) The networks used by the virtual machine are also virtualized by the underlying ESX Server. This ensures that even after the migration, the
virtual machine network identity and network connections are preserved. V-Motion manages the virtual MAC address as part of the process. Once
the destination machine is activated, V-Motion pings the network router to ensure that it is aware of the new physical location of the virtual MAC
address. Since the migration of a virtual machine with V-Motion preserves


####We select the tab Configuration-> Network Adapters and we see that we have visibility of the new connections.####

01.![](http://i.imgur.com/CVFreDQ.png)

Now we look at the tab Configuration-> Networking

02.![](http://i.imgur.com/Sj72oiv.png)

Click on Add Networking to create the vSwitch.

03.![](http://i.imgur.com/RPahDUp.png)

04.Select VMKernel and click on Next.

05.![](http://i.imgur.com/fJhPiT5.png)

Making a network card or cards that have connected from one server to another (in our case vmnic9) And click on Next.

06.![](http://i.imgur.com/EFZnrfZ.png)

We set Use this port group for vMotion.

We wrote a Label Network different if you want (optional) and click on Next. We for example we put Vmotion.

07.![](http://i.imgur.com/kBaVzf4.png)

We set Use the following IP settings:

- IP Address: 50.50.50.1
- Subnet Mask: 255.255.255.252

Click on Next.

08.![](http://i.imgur.com/28v5cTz.png)

Click on Finish.

09.![](http://i.imgur.com/an8v0uq.png)

We found that they have created a new virtual switch with Vmotion.

We connect to another server involved.

We select the tab Configuration-> Network Adapters and we see that we have visibility of the new connections.

10.![](http://i.imgur.com/GS2cEoR.png)

Now we look at the tab Configuration-> Networking

11.![](http://i.imgur.com/ua4lchq.png)

Click on Add Networking to create the vSwitch.

12.![](http://i.imgur.com/O3NMVko.png)

Select VMKernel and click on Next.

13.![](http://i.imgur.com/5fE55IV.png)

Making a network card or cards that have connected from one server to another (in our case vmnic9) And click on Next.

14.![](http://i.imgur.com/lAtLFBM.png)

We set Use this port group for VMotion.

We wrote a Label Network different if you want (optional) and click on Next. We for example we put Vmotion.

![](http://i.imgur.com/cvEDKmC.png)

We set Use the following IP settings:

- IP Address: 50.50.50.2 (This ip must be different from the server that we configured earlier 1).
- Subnet Mask: 255.255.255.252 (Since we will use only 2 ip's).

Click on Next.

15.![](http://i.imgur.com/zNtkPHz.png)

Click on Finish.

And now what we will do to ensure that the entire system is working properly migrate a VM from one ESXi to the other using Vmotion functionality you just configured.

We press the right mouse button on a virtual machine.

16.![](http://i.imgur.com/7qTdkaJ.png)

Click on Migrate.

17.![](http://i.imgur.com/jVn91H5.png)

Click on Next.

18.![](http://i.imgur.com/B9qrhAs.png)

Select the target server where we will move the virtual machine.

Click on Next.

19.![](http://i.imgur.com/GT8skf0.png)

Click on Next.

20.![](http://i.imgur.com/8HGdTSP.png)

Click on Finish to start the migration.

21.![](http://i.imgur.com/sS69GH9.png)

Perfect the system has been migrated from an ESXi host to another without losing the service and in just 41 seconds, if we set up another network card this time has reduced considerably, as we have said before Vmotion is able to use multiple cards network for migration.