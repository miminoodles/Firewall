# FortiGate Firewall Deployment and WAN-LAN Network Configuration in VMware Lab
# Project Introduction

In this project, I deployed and configured a FortiGate virtual firewall inside a VMware Workstation lab environment. The goal was to simulate a real-world enterprise firewall deployment, including WAN configuration, LAN segmentation, NAT implementation, firewall policy creation, and internal client internet access validation.

This project demonstrates hands-on experience in firewall administration, traffic monitoring, and security policy enforcement.
# Project Overview

This lab simulates a small enterprise network where:
-	A FortiGate VM acts as the perimeter firewall.
-	The WAN interface connects to the internet via a bridged adapter.
-	The LAN interface provides internal network segmentation.
-	A Windows 10 machine acts as an internal client.
-	Firewall policies are created and monitored.

The project validates:
-	WAN to LAN routing
-	Policy-based traffic control
-	Interface configuration via CLI and GUI
-	Real-time traffic logging

# Project Objectives

The objectives of this deployment were to:
-	Deploy FortiGate VM in VMware
-	Configure WAN interface using DHCP
-	 Configure LAN interface using static addressing
-	 Create and enable firewall policies
-  Connect internal client to LAN segment
- Validate connectivity using ping and browser tests
-	Monitor traffic logs and byte counters

# Project Scope

This lab focuses on:
-	Basic firewall deployment
-	WAN/LAN interface configuration
-	Manual IP addressing
-	Traffic validation

Advanced features such as VPN, IPS, VLAN segmentation, and web filtering were not implemented in this phase but can be added in future lab expansions.

# Technologies & Tools Used
-	VMware Workstation
-	FortiGate VM (FortiOS)
-	Windows 10 Virtual Machine
-	Bridged Networking
-	LAN Segmentation
-	CLI Configuration
-	GUI Policy Management

# Project Validation

Connectivity was successfully validated through:
-	Successful ping from internal client to LAN gateway
-	GUI access via HTTP/HTTPS
-	Internet browsing from internal VM
-	Traffic monitoring via policy byte counter increase

# Text Guide
# Step 1: Create Fortinet Account & Download VM
	1.	Open your web browser.
	2.	Go to: support.fortinet.com
	3.	Register for an account (it’s free).
	4.	Use a valid email (you’ll receive an activation code).
	5.	Enter verification code from your email.
	6.	Complete captcha.
	7.	Set your password.
	8.	Fill in required account information.
	9.	Accept terms and conditions.
	10.	Click complete.

Now login using your email and password.

# Step 2: Download FortiGate VM Image
	1.	Go to Support
	2.	Under Select Product, choose VM Images
	3.	Choose:
	•	Product: FortiGate
	•	Platform: VMware ESXi
	4.	Download the latest version.
	5.	Save it to your desired folder.
	6.	Extract the compressed file.

# Step 3: Import FortiGate into VMware
	1.	Open VMware Workstation.
	2.	Click Open a Virtual Machine.
	3.	Locate extracted files.
	4.	Select: fortigate_vm64.ovf
	5.	Click open.
	6.	Accept license agreement.
	7.	Enter VM name.
	8.	Modify storage path if needed.
	9.	Click Import.
	10.	Wait for completion.

  # Step 4: Modify Network Adapters (Initial Setup)
	1.	Click Edit Virtual Machine Settings.
	2.	Go to Network Adapter 2.
	3.	Select LAN Segment.
	4.	Create LAN Segment 1.
	5.	Click OK.

Now:
-	Uncheck Connect at power on for Network Adapter 1.
-	Do same for other unused adapters.
- Click OK

# Step 5: Configure Bridged Network
	1.	Go to Edit → Virtual Network Editor.
	2.	Click Change Settings.
	3.	Locate VMnet0 (Bridged).
	4.	Under “Bridged to”, change from Automatic.
	5.	Select your physical ISP adapter.
	6.	Click Apply.

# Step 6: First Boot
	1.	Power on FortiGate VM.
	2.	Default login:
	•	Username: admin
	•	Password: (blank)
	3.	Press enter.
	4.	Set new password.

# Step 7: Disable NTP (Initial)
- #config system ntp
- #set ntpsync disable
- #set type custom
- #end

Shut down VM.

# Step 8: Enable Network Adapters Properly
	1.	Edit VM settings.
	2.	Enable Connect at power on for:
	•	Network Adapter (WAN)
	•	Network Adapter 1 (LAN)
	3.	Leave others disabled.
	4.	Power on VM.
	5.	Login.

  # Step 9: Enable NTP Properly
- #config system ntp
- #set ntpsync enable
- #set type fortiguard
- #end

# Step 10: Configure WAN Interface (Port1)
- #config system interface
- #edit port1
- #set mode dhcp
- #set role wan
- #set alias WAN
- #set allowaccess http https telnet ssh ping
- #show
- #next

# Step 11: Configure LAN Interface (Port2)
- #edit port2
- #set mode static
- #set role lan
- #set alias LAN
- #set ip 10.10.10.1/24
- #set allowaccess http https telnet ssh ping
- #show
- #end

To verify:
- #show system interface
- #get system interface physical

# Step 12: Test WAN Connectivity

From your main PC CLI: 
-	Ping Port1 IP address.
-	You should receive replies.

Then open browser:
- Enter Port1 IP address.
- Login via GUI.
  
# Step 13: Create Firewall Policy

Go to:
Policy & Objects → Firewall Policy → Create New

Set:
-	Name: LAN-Internet
-	Incoming: Port2 (LAN)
-	Outgoing: Port1 (WAN)
-	Source: All
-	Destination: All
-	Schedule: Always
-	Services: All
-	Enable NAT
-	Log allowed traffic: All sessions
-	Enable policy

Click OK.

# Step 14: Connect Windows 10 to LAN
	1.	Open Windows 10 VM.
	2.	Edit VM settings.
	3.	Network Adapter → Select LAN Segment 1.
	4.	Power on.

Since DHCP not enabled, configure manually:

Go to:
ncpa.cpl on the cli, right click on your Ethernet, click on the TCP/IP V4 and right click on the properties 

Set:
-	IP: 10.10.10.10
-	Subnet: 255.255.255.0
-	Gateway: 10.10.10.1
-	DNS: 8.8.8.8
-	Alt DNS: 1.1.1.1

Click OK.

# Step 15: Verify Connectivity
	1.	Open windows CMD.
	2.	Ping:
ping 10.10.10.1

 3.	Open browser.
 4.	Access: http://10.10.10.1
 5.	Login.

# Step 16: Test Internet
	1.	Open YouTube.
	2.	Confirm internet works.
	3.	Go back to FortiGate.
	4.	Refresh firewall policy.
	5.	Observe traffic bytes increasing.

This confirms:
-	NAT working
-	Firewall policy working
-	Internal device accessing internet
-	Logging working


# What FortiGate Firewall Can Do (Beyond Basic Policy)
# Advanced Firewall Policies

You can create policies based on:
-	 Source & destination IP
-	 User identity (AD integration)
-	 Time schedule
-	 Application type
-	 Device type
-	 Geo-location (block countries)
-	 VLAN segmentation

You can also:
-	Enable logging per session
-	Apply security profiles per policy
-	Create deny policies
-	Create DMZ policies

# NAT Types

Not just basic NAT.
-	Source NAT (SNAT)
-	Destination NAT (DNAT)
-	VIP (Virtual IP) for port forwarding
-	Static NAT
-	Policy NAT

Example:
-	Host a web server internally
-	Use VIP to expose it to internet
 
#  Intrusion Prevention System (IPS)

FortiGate can:
-	Detect exploits
-	Block malicious traffic
-	Stop known attack signatures
-	Protect against SQL injection
-	Block brute-force attacks

You can enable IPS profile inside firewall policy.

# Web Filtering

You can:
-	Block adult content
-	Block social media
-	Block gambling
-	Allow only educational sites
-	Create custom URL filters
-	Enable SafeSearch enforcement

# Application Control

Instead of blocking entire websites, you can block:
-	YouTube but allow Google
-	WhatsApp Web only
-	BitTorrent
-	Zoom
-	Facebook chat only

# Antivirus (Gateway Level)

FortiGate can:
-	Scan HTTP/HTTPS traffic
-	Scan FTP
-	Detect malware downloads
-	Block infected files

# SSL Inspection

It can:
-	Decrypt HTTPS traffic
-	Inspect encrypted threats
-	Apply IPS inside encrypted traffic

# VPN Configuration

You can configure:

- IPsec VPN (Site-to-Site)

Connect two offices securely.

- SSL VPN (Remote Access)

Allow remote users connect securely.

You can:
-	Create user groups
-	Assign policies to VPN users
-	Control what VPN users access


