# OpenDaylight using AWS vMX Hosted Instance

1 Creation of vMX Instance




● From EC2 dashboard select Launch Instance from the Create Instance section
● In the search panel, enter vmx
● Select the “2 results in AWS Marketplace”
● Select the free trial version
● Use the M4 Extra Large version - m4.4xlarge
● Hit Next: Configure Instance Details
● You should select your default OOBM - e.g. us-east-2a (you want all of your instances in the same availability zone)
● Hit Next: Add Storage
● Hit Next: Add Tags
● Hit Next: Configure Security Group
● If this is the first setup you’ve done, you may want to create a new security group.
If you’re adding an instance to an existing set of devices, you’ll likely select an existing security group
● Hit: Review and Launch
● If this isn’t the first instance, it will indicate that the instance isn’t available for free tier usage
● Hit: Launch
● Choose to create a new key pair or use an existing key pair







Limitations of vMX in AWS are specified here:
https://www.juniper.net/documentation/en_US/vmx/topics/concept/vmx-aws-overview.html




2 Initial Login and Configuration

The instance will spin up with a public IP to start with - once more interfaces are added, a public IP won’t be assigned automatically.

Ensure that you’ve downloaded the key.pem file to your environment and then ssh to the vMX:

ssh -i /pathtokey/key.pem jnpr@public_ip




Note: the following sections cover the initial creation of the first VMX instance, however, once you have the first one created, you can dump the configuration, alter the host-name and remove the fxp0 interface (which is unique to each device) and then use the instructions in the
2.1 Set the root password
Before you can commit changes to the configuration, you must set the root password.




In our case we use the standard lumina1 as the password.




configure
set system root-authentication plain-text-password




commit

2.2 Configure for NETCONF access




configure
set system services netconf ssh
set system services netconf ssh port 830
set system services netconf yang-compliant
set system services netconf rfc-compliant




commit

2.3 Copy public key from LSC to the VMX

To enable SSH communication with the VMX, you’ll have created a keypair on the LSC machine.

Copy the public key to the VMX jnpr account using the private IP address of the VMX (not the public IP listed in the EC2 dashboard!).




● login into the LSC
● execute a secure copy along the lines of:
scp -i ./kp-vmw-01.pem ./.ssh/lsc-aws.pub jnpr@172.31.14.63:~







kp-vmw-01.pem will be the generated .pem file from AWS
lsc-aws.pub will be the public key you generated




This will place the .pub file in: /var/home/jnpr on the VMX




You’ll use the lsc-aws.pub when creating the lumina user.
2.4 Create the lumina user
This is the user we’ll use for our LSC interactions e.g. mounting/configuring the device.




Set the password to: lumina1




configure
edit system login
edit user lumina
set full-name "LSC NETCONF Access"
set class superuser
set authentication plain-text-password


set authentication load-key-file lsc-aws.pub







2.5 Bulk alteration based on template
2.5.1 Create the template




Once you’ve done a basic configuration of a device, you can export the config to a text file and make adjustments for new devices.

Start by showing the config without piping the output to less:

show configuration | no-more

Copy the output to a text file.

Remove the fxp0 section - this is the default interface, which is unique to each device:

    interfaces {
        fxp0 {
            unit 0 {
                family inet {
                    address 172.31.14.194/20;
                }
            }
        }
    }


Edit the host-name to reflect the new device (there are a couple of locations).




system {
host-name awsvmx01;







2.5.2 Use the template
Login to the target system




Ensure that you’ve set the root password to allow commits.




edit
load merge terminal
<paste in the altered template - hit cntrl-D to finish>







show | compare
commit confirmed
commit
exit




show interfaces terse

Now, head to EC2 dashboard and stop the instance.

At this point you should assign the network interfaces to the device - this must be done in the same order as the interfaces you’ve defined on the device i.e. the various xe-#/#/# interfaces will be configured with the addresses in the order that are attached to the device.

Then, start the instance.

Afterwards, log back in and the show interfaces terse should show the xe interfaces.




xe-0/0/0 up up
xe-0/0/0.0 up up multiservice
xe-0/0/1 up up
xe-0/0/1.0 up up multiservice
xe-0/0/2 up up
xe-0/0/2.0 up up multiservice
xe-0/0/3 up down
xe-0/0/3.0 up down multiservice




And now you can assign the addresses to the interfaces.




edit
edit interfaces
set xe-0/0/0 unit 0 family inet address 172.31.22.165/20
set xe-0/0/1 unit 0 family inet address 172.31.38.121/20
set xe-0/0/2 unit 0 family inet address 172.31.67.195/20
show
commit
exit
show interfaces terse




2.6 Setting up GRE Tunnels




edit
edit chassis
set fpc 0 pic 0 tunnel-services bandwidth 10g
top
show | compare
commit
exit




show interfaces terse

For each LINK interface, we’ll create a generic routing interface that defines a tunnel between the IP of the source interface and the IP of the destination interface.

We’ll add a description that indicates the intended purpose e.g. which LINK is supported.

Note: we’ll also specify the unit identifier so that it aligns to LINK name i.e. in the following example, we’re using unit 1 because it’s LINK01 we’re creating the tunnel for.




edit
edit interfaces gr-0/0/0 unit 1
set description "LINK01 overlay"
set tunnel source destination path-mtu-discovery




Next, we’ll add a local IP address based on changing the first tuple of the local xe interface. For example, if the local xe address is 172.31.22.165, we’ll add inet address 10.31.22.165/20




set family inet address 10.31.22.165/20
set family inet mtu 1400







up
show




Should show something like:




unit 1 {
description "LINK01 overlay";
tunnel {
source 172.31.22.165;
destination 172.31.111.75;
path-mtu-discovery;
}
family inet {
mtu 1400;
address 10.31.22.165/20;
}
}




2.6.1 GRE Unit Templates

You create the “units” for each link and load them as follows:




edit
edit interfaces gr-0/0/0
load merge terminal relative




show
top
commit




2.7 Setting up OSPF




edit
edit protocols ospf
set area 0.0.0.0 interface gr-0/0/0.1 interface-type p2p
??? do this for each gr-0/0/0.X ???
show
top
commit







show ospf neighbor
show ospf interface
show configuration protocols




2.8 Setting up BGP




https://www.dummies.com/programming/networking/juniper/how-to-configure-bgp-on-a-junos-network/
https://www.juniper.net/documentation/en_US/junos/topics/reference/configuration-statement/local-address-edit-protocols-bgp.html




set up the local-address




See the following for BGP setup on LSC
https://docs.opendaylight.org/en/stable-nitrogen/user-guide/bgp-user-guide.html




2.9 Setting up the Internet Gateway (IGW)

https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html

2.10 Checking service status stuff

show route table service1

show mpls lsp

show mpls lsp name service1-VMX-04 extensive

show route summary

show route table Service-2-CustomerB.inet.0

show route table Service-2-CustomerB.inet.0 172.31.96.0/20

show mpls lsp name service1-VMX-04 statistics

show interfaces xe-0/0/1.222

show interfaces xe-0/0/1.222 terse

show bgp summary

show route advertising-protocol bgp 10.0.0.1 172.32.112/20

2.11 Groups and inheritance

We’re using configuration groups to facilitate changes in multiple places in the configuration hierarchy.

When groups added to apply-groups - the configuration in those groups is added to the master configuration.

When you do a show configuration, though, you don’t see the merged configuration.

If you want to see the merged form of the configuration, you can do the following:

show configuration interfaces xe-0/0/1 | display inheritance

Or, for the entire configuration:

show configuration | display inheritance







3 AWS Networking Aspects
Useful subnet calculator: http://www.subnet-calculator.com/
And cheatsheet: https://systembash.com/cidr-cheatsheet/




Generally, we want separate subnets for each our links. We set this up through the VPC.

Services->Network & Content Delivery->VPC

This brings you to Resources by Region.

We’re working with 172.31.0.0/16

Click on the Subnets to create additional subnets.

It’s important to ensure that all subnets are created in the same availability zone.

Once the subnets are created they can be used in the creation of the network interfaces; head back to the EC2 dashboard and head to:

Network & Security->Network Interfaces

Click on the Create Network Interface button and you’ll get:

The subnets you created are listed here - select the one that’s appropriate for the LINK you’re creating between devices.

4 Postman Collection and Environment

SErvices->networking->vpc

eth1 -change subnet - click network interface id




edit
load merge terminal




(there’s also load merge patch)




paste in config
cntrlD




show | compare

assign address to interfaces

Can’t use OSPF without GRE because EC2 networking doesn’t support multicast




fire up ospf
show ospf neighbor




In AWS only!!!

Set up GRE




edit
edit chassis
set fpc 0 pic 0 tunnel-services bandwidth 10g
top
show | compare




edit interfaces gr-0/0/0

load merge terminal relative

5 Host device setup




EC2
ubuntu 16.04
m5a.large
Start in the OOBM Default subnet
Add to the existing security group







IP Command cheatsheet
https://access.redhat.com/sites/default/files/attachments/rh_ip_command_cheatsheet_1214_jcs_print.pdf




5.1 Set the host names




set the hostnames - for convenience
sudo vi /etc/hostname
sudo vi /etc/hosts
(copy the loopback line and put in selected hostname)




5.2 Install iperf




sudo apt-get update
sudo apt-get install iperf
https://www.linode.com/docs/networking/diagnostics/install-iperf-to-diagnose-network-speed-in-linux/







on host01 start iperf server:
iperf -s







on host02 start iperf client
iperf -c




Note: do your apt-get before attaching the new interfaces!




5.3 Additional interfaces
With instructions from:
https://aws.amazon.com/premiumsupport/knowledge-center/ec2-ubuntu-secondary-network-interface/







Create network interfaces for each host on the appropriate subnets for the intended LINKs.
Create it in the PrivateLinks security group




Note: if you need internet access to install any additional software, do it BEFORE you add the addition interfaces!




Login in to the host instance and run:
ip a | grep [1]







You’ll see something like:
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
2: ens5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq state UP group default qlen 1000




Attach the interface - Note: there’s no need to restart the instance




List the interfaces again and you’ll see:
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
2: ens5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq state UP group default qlen 1000
3: ens6: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000







Create interface config file:
sudo vi /etc/network/interfaces.d/51-ens6.cfg




Paste the following (where ip-address comes from your newly added interface):




auto ens6
iface ens6 inet static
address ip-address
netmask 255.255.240.0




Gateway configuration
up ip route add default via 172.31.16.1 dev ens6 table 1000 (don’t include)
Routes and rules




up ip route add ip-address dev ens6 table 1000
up ip rule add from ip-address lookup 1000




Create the restrict-default-gw file

sudo vi /etc/dhcp/dhclient-enter-hooks.d/restrict-default-gw

Enter the following




e ${interface} in
ens5)
;;
*)
unset new_routers
;;
esac




6 Network Topology

https://www.lucidchart.com/documents/edit/15050f60-c5be-4c88-8439-0a4055a7d7ea/0

7 Python notes

sudo -H /usr/bin/python -m pip install --upgrade pybase64

sudo -H /usr/bin/python -m pip install dpath

sudo -H /usr/bin/python -m pip install requests

Make sure you prevent buffering of print statements by doing the following in your execution shell:

export PYTHONUNBUFFERED=TRUE

7.1 SSH access via Python

https://stackoverflow.com/questions/6203653/how-do-you-execute-multiple-commands-in-a-single-session-in-paramiko-python

Use the code in the final answer that defines the ssh class

8 Cisco CSR

Usage instructions: https://aws.amazon.com/marketplace/usageinstructions?ref=cns_1clkUsg&product=9f5a4516-a4c3-4cf1-89d4-105d2200230e&version=16.9.1




Using your keypair, login as follows (for example):
ssh -i ~/kp-vmw-01.pem ec2-user@172.31.2.53







Some useful info on using Python to access the CSR
https://www.fir3net.com/Networking/Concepts-and-Terminology/how-to-configure-a-cisco-csr-using-netconf-yang.html







Configuring netconf over ssh
https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/cns/configuration/15-mt/cns-15-mt-book/netconf-sshv2.html




https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/sec_usr_cfg/configuration/xe-16/sec-usr-cfg-xe-16-book/sec-cfg-sec-4cli.html#GUID-16701208-7601-4C7B-8A41-E70C11258A09

CLI configuration fundamentals

https://www.cisco.com/c/en/us/td/docs/ios/ios_xe/fundamentals/configuration/guide/2_xe/cf_xe_book/cf_cli-basics_xe.html

Setting password - don’t think this worked

SUMMARY STEPS

enable
configure terminal
enable password password
end
exit
enable




configure terminal
hostname
exit







configure terminal
netconf-yang
netconf-yang feature candidate-datastore
exit







Example session:
CSR-01#configure terminal
Enter configuration commands, one per line. End with CNTL/Z.
CSR-01(config)#netconf-yang
CSR-01(config)#netconf-yang feature candidate-datastore
netconf-yang initialization in progress - datastore transition not allowed, please try again after 30 seconds
CSR-01(config)#
CSR-01(config)#netconf-yang feature candidate-datastore
netconf-yang and/or restconf is transitioning from running to candidate
netconf-yang and/or restconf will now be restarted, and any sessions in progress will be terminated
CSR-01(config)#







show netconf-yang datastores
show netconf-yang sessions
show netconf-yang sessions detail
show platform software yang-management process




show netconf schema

This let’s you test the netconf connection:

ssh -2 -vvv -s lumina@10.10.51.64 -p 830 netconf




8.1 Create the lumina user
http://www.mustbegeek.com/enable-ssh-in-cisco-ios-router/







configure terminal
username lumina password lumina1
username lumina privilege 15
exit







To reboot the router use
reload




Changes to the config take place immediately, but to save the changes across restarts do:

copy running-config startup-config




Yang Models
https://github.com/YangModels/yang




show version

Cisco IOS XE Software, Version 16.09.01

8.2 Operational Data Polling

https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/prog/configuration/166/b_166_programmability_cg/operational_data_parser_polling.html

8.3 Debugging netconf

https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/cns/configuration/xe-3s/cns-xe-3s-book/cns-netconf.html




enable
debug netconf all
terminal monitor




8.4 BGP related

https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/iproute_bgp/configuration/xe-16/irg-xe-16-book/irg-xe-16-book_chapter_01010101.pdf

https://learningnetwork.cisco.com/blogs/vip-perspectives/2017/12/14/demystifying-bgp-session-establishment

Configure OSPF to distribute link-state




configure terminal
router ospf #
distribute link-state




9 VyOS Router

VMWare is using opensource VyOS router for tweaking latency and jitter aspects.

You can spin them up in AWS, just do launch instance and search for “vyos”

Login in as vyos@x.x.x.x using your keypair.

Usage info at: https://wiki.vyos.net/wiki/User_Guide




Create a lumina user:
https://wiki.vyos.net/wiki/User_Guide#Creating_Login_User_Accounts







configure
set system login user lumina
set system login user lumina authentication plaintext-password lumina1
delete service ssh disable-password-authentication
commit
exit




Note: if you don’t remove the disable-password-authentication, you won’t be able to login to ssh using a password

Similar OS to Juniper OS - has configure mode




Copy the LSC public key to vyos - e.g.
scp -i ~/kp-vmw-01.pem ./lsc-aws.pub vyos@172.31.12.2:~




Set the publickey for lumina




configure
loadkey lumina lsc-aws.pub
commit
exit







Can’t seem to find netconf stuff. It’s alluded to here in service/netconf
ftp://ftp.networking.ibm.com/cloud/bluemix/network/vra/vnf-platform-5.2r1-automated-provisioning.pdf







And this seems to put a nail in the coffin:
https://napalm.readthedocs.io/en/yang_doc/support/vyos.html




No support for netconf
