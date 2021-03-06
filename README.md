# opendaylight-setup
This directory contains setup scripts for ODL.

Start with a Linux host or VM with git and java installed (at a minimum - ideally mininet, openconnect, pip, pyang etc. too, depending on what features you plan to test).

Installation Instructions:

1.  Clone this repo:

  git clone https://github.com/CiscoDevNet/opendaylight-setup.git ODL

2.  Within the ODL directory create an "images" subdirectory:

  cd ODL<br>
  mkdir images
 
  So you will now have 2 subdirectories:

  * python
  * images

3.  Copy the appropriate OpenDaylight distribution file (.tar.gz) from https://www.opendaylight.org/downloads (or other location) to images:

  Example: (Assumes you are downloading the Oxygen-SR1 release.)

  wget -P images https://nexus.opendaylight.org/content/groups/public/org/opendaylight/integration/karaf/0.8.1/karaf-0.8.1.tar.gz

4.  Under the "ODL" directory, edit the DISTRO and VERSION variables in the "parameters" file to reflect the release you just downloaded into the "images" directory.
 
5. Unpack ODL using

	./unpack-odl

6.	If you are *not* using the dCloud infrastructure (http://dcloud.cisco.com) to experiment with ODL, edit the "nodes" file to reflect the list of nodes and their IP Addresses in your VIRL simulation.

7. If you are *not* using dCloud and if you plan to use BGP, edit the parameters file to change the IP address of the BGP speaker.

8.	Optionally edit the parameters file to change the set of features installed at ODL startup.

9.	Optionally edit the logs file to change the set of additional logging activated at ODL startup.
 
10. Set up ODL using (this loads key features and logging configs)

  ./setup-odl
   
11.	If you are using dCloud from Linux then set up the VPN using:

	sudo ./start-vpn site username password
	
	* site is one of sjc, rtp, lon, sng or chi
	* username and password can be found in your dCloud session details

	(note that your unix account will need sudo privileges)
	
12. If using dCloud from a Mac then use the Cisco AnyConnect VPN client.   Set the host to https://dcloud-xxx-anyconnect.cisco.com and substitute sjc, rtp, lon, sng or chi for xxx. 

13.	Start ODL using

  ./start-odl

14.	Configure ODL using

  ./config-odl interface (e.g. tun0, eth0)
  
  (this will connect ODL to the NETCONF nodes and configure BGP-LS/PCE-P - if the features are selected)
  
The repository also contains a subdirectory "vagrant" which contains a Vagrantfile and bootstrap.sh script.

If you have Vagrant and VirtualBox or VMWare Workstation/Fusion installed you can do a "vagrant up" from that directory and a VirtualBox VM will be created consisting of:

* Ubuntu 16.04
* git
* zip
* openconnect (required if using dCloud)
* java (JRE only)
* mininet (required if testing OpenFlow)
* pip
* pyang
* this repository
* OpenDaylight Oxyten SR1

ODL will be unpacked.  So you can follow the instructions above from step 6.

Note that the Vagrantfile is currently configured to allocate 2 vCPUs and 8GB of RAM to the VM.   If your machine only has 8GB of RAM then you may wish to allocate 4GB of RAM.  Likewise if you only have 2 CPU cores you may wish to allocate 1 vCPU.   Equally if you want to use a different hypervisor you will need to edit the Vagrantfile.

The repository also contains a subdirectory "xrvr-5" with KVM scripts etc. for setting up a 5 node XRv lab.

### Scripts are:

**unpack-odl** unpacks the .tar.gz file.  Creates a new subdirectory for the ODL distro.

**setup-odl** sets up logging/features for ODL

**start-vpn** connects to dCloud VPN.  Takes 3 parameters:  

* site (sjc/rtp/lon/sng/chi)
* username
* password

**stop-vpn** disconnects from dCloud VPN

**start-odl** cleans out data from previous runs and starts ODL

**stop-odl** stops ODL

**config-odl** sets up NETCONF nodes, BGP etc. - uses scripts from the python subdirectory (plus in the dCloud case REST calls to dCloud APIs)

**delete-odl** deletes the ODL distribution

**restart-odl** restarts ODL by calling other scripts

### Additional files are:

**parameters** parameters - encoded as environment vars:

* DISTRO (name of ODL distribution)
* VERSION (release of ODL - e.g. Oxygen)
* BGP_PEER (IP address of BGP peer)
* BGP_NODE (NETCONF name of BGP peer)
* BGP\_NEXT_HOP (next-hop from BGP peer towards ODL - only required if ODL is on a different subnet from the BGP peer)
* LOCAL_AS
* REMOTE_AS
* ODL_USER
* ODL_PASS
* NETCONF_PORT
* NETCONF_USER
* NETCONF_PASS
* FEATURES (list of features to add to ODL's default set)

**logs** extra logs to activate

**nodes** list of NETCONF nodes to mount (each line consists of a node name and node IP address)

**nodes.16** / **parameters.16**  16-node setup for RTP dCloud 

### Python scripts are:

**del-node.py** unmounts a node

**put-node.py** mounts a node

**is-node-connected.py** checks if a node is mounted

**put-bgp-rib.py** configures the ODL BGP RIB

**put-app-rib.py** configures the ODL App RIB

**put-bgp-peer.py** configures a BGP peer in ODL

**put-bgp-neighbor.py** configures an XR router to peer with ODL

**put-pcep-client.py** configures an XR router to act as a PCC towards ODL

**put-static-route.py** configure a static route on an XR router
