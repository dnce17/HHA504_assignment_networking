# HHA504 Networking Assignment

## Azure
### Network Creation
1. Created VNet network name
![Created VNet network name](azure/network_creation/network_creation_1.png)
2. Created VNet network subnet
![Created VNet network subnet](azure/network_creation/network_creation_2.png)
3. VNet network was successfully created
![VNet network creation success](azure/network_creation/network_creation_3.png)

### Assign a Dedicated or Use Dynamic IP
1. Created a static public IP address
    * Basics tab
        * IP Version: IPv4
        * SKU: Standard
        * Availability Zone: Zone-redundant
        * Tier: Regional
        * IP address assignment: Static
        * Routing preference: Microsoft network
    * Left everything else as default and created the IP address
![created static public IP address](azure/ip_reservation/reservation.png)
2. Created VM
    * Basics tab
        * Security Type: Standard
        * Image: Ubuntu
        * VM architecture: Changed VM architecture to Arm64
        * Size: Standard_B2pts_v2 - 2 vcpus, 1 GiB memory ($6.13/month)
        * Authentication type: Password
        * Select inbound ports: HTTP (80), HTTPS (443), SSH (22)
    * Disks
        * OS disk size: Standard
    * Networking:
        * Virtual network, Subnet, Public IP --> set these three to the VNet, subnet, and IP made before

### Configure Firewall Settings
1. Clicked the VM instance 
![Clicked the VM instance](azure/firewall/firewall_1.png) 
2. Clicked "Networking settings," 
3. Clicked "Create port rule," then "Inbound port rule"
![Click networking settings, create port rule, then inbound port rule](azure/firewall/firewall_2.png) 
4. Changed "Destination port ranges" to the port that will be used (5007 in my case), then click "Add"
![Change destination port ranges to the port that will be used](azure/firewall/firewall_3.png) 
5. Check to make sure the port was added successfully
![Check that port was added successfully](azure/firewall/firewall_4.png) 


### Steps and Screenshots to Map IP Address to Domain
1. Created a record in Namecheap and...
    * added a host name
    * pasted in the public IP from the GCP VM instance
    * set the TTL to lowest as possible (1 min in this case)
    * saved all changes
![record added in Namecheap's domain setting](azure/dns/namecheap.png)
2. Opened up terminal in own desktop and ran the follow commands (cmds):
    * ssh azureuser@20.84.76.176
        * Format: (VM username)@(VM IP address)
    * sudo apt-get update
    * python3
        * Purpose is to see if it exist
    * exit()
        * Exits python terminal
    * pip3
        * Checks if it exist. It did not, so the next cmd was ran.
    * sudo apt install python3-pip
    * git clone https://github.com/hantswilliams/hha-504-flask-networking.git
    * cd hha-504-flask-networking
    * sudo apt install python3.12-venv
        * Needed to create a venv; Azure needs a venv to install packages from requirements.txt
    * python3 -m venv danny
        * Format: python3 -m venv (desired name for this venv)
    * source danny/bin/activate
        * Activates venv
    * pip3 install -r requirements.txt
    * python3 app.py
3. Typed 34.28.213.127:5007 into one tab and http://flask.dche.me:5007 in another. Both successfully displayed.
    * KEY NOTE: adding http:// prior to subdomain name is required or else the site will not connect

## GCP
### Network Creation
1. Created VPC network name
![Created VPC network name](gcp/network_creation/network_creation_1.png)
2. Created VPC network subnet
![Created VPC network subnet](gcp/network_creation/network_creation_2.png)
3. VPC network was successfully created
![VPC network creation success](gcp/network_creation/network_creation_3.png)

### Assign a Dedicated or Use Dynamic IP
1. In VM creation, changed operating system to Ubuntu
![Changed operating system to Ubuntu](gcp/ip_reservation/make_vm_1.png)
2. Turned on HTTP and HTTPS traffic
![Turn on HTTP and HTTPS traffic while creating virtual machine](gcp/ip_reservation/make_vm_2.png)
3. VM was successfully made
4. For this assignment, the dynamic public IP assigned by Azure was used
![Virtual machine successfully made](gcp/ip_reservation/public_ip.png)

### Configure Firewall Settings
1. Typed "firewall" in GCP search bar and clicked "Firewall VPC Network"
![Clicked "firewall" in results of search bar](gcp/firewall/go_to_firewall.png)
2. Clicked "Create Firewall Rule" and configured it with the following:
    * added a name
    * "Targets" header --> All instances in the network 
    * "Source IPv4 ranges" header --> 0.0.0.0/0
    * Checked the "TCP" box and entered the port number that the Professor's Flask app from his hha-504-flask-networking repo is using, or 5007
        * This repo will be cloned later on
![configured firewall rule](gcp/firewall/rule_config.png)
3. Clicked "Create" and the rule was successfully made
![clicked "create" to create firewall rule](gcp/firewall/rule_created.png)

### Steps and Screenshots to Map IP Address to Domain
1. Created a record in Namecheap and...
    * added a host name
    * pasted in the public IP from the GCP VM instance
    * set the TTL to lowest as possible (1 min in this case)
    * saved all changes
![record added in Namecheap's domain setting](gcp/dns/namecheap.png)
2. Clicked "SSH" of the VM instance to open up a shell
![ran SSH of VM instance](gcp/dns/ran_ssh.png)
3. In the shell, the following commands (cmds) were ran in the order below:
    * sudo apt-get update (NOTE: always run first when opening SSH to avoid potential errors later on)
    * sudo apt install python3-pip
    * git clone https://github.com/hantswilliams/hha-504-flask-networking.git
        * This repo contains the HTML that will appear after mapping is completed
    * cd hha-504-flask-networking
    * pip3 install -r requirements.txt
    * sudo ufw allow 5007
    * sudo ufw allow 80
    * sudo ufw allow 443
    * python3 app.py
5. After running the last cmd, I typed 34.28.213.127:5007 into the search bar of one tab and python.dche.me:5007 of another tab. Both successfully displayed the HTML from the cloned repo.
    * NOTES
        * The website seemed to have taken a few minutes to successfully appear. While waiting, I used a [DNS lookup site](https://www.whatsmydns.net/dns-lookup/a-records) to see if the record has propagated across the Internet.
            * I typed in "dche.me" in the website bar and viewed results globally 
        * 34.28.213.127:5007 follows format of (ip address):(port number)
![HTML successfully displayed after entering 34.28.213.127:5007 to search bar](gcp/dns/enter_ip_n_port.png)
        * python.dche.me:5007 follows (host name).(domain name):(port number)
![HTML successfully displayed after entering python.dche.me:5007 to search bar](gcp/dns/enter_domain_n_port.png)