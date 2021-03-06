## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.



![Relative path to image file](../../blob/main/Images/Project%201%20Network%20Diagram.drawio.png)




These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the playbook file may be used to install only certain pieces of it, such as Filebeat.

  - install-elk.yml

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting external access via public IP to the network.
- Load balancers protect the availability aspect of security. Using a pool of servers to address incoming traffic, they mitigate Dos attacks
  Using a jump box, the enviroment allows control of the application deployment for our network.  Using Docker to configure and update our environment, gives flexibility and consisentcy with the 
  applications installation.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the enviroment and system logs.
- Filebeat helps generate and organize log files to send to Logstash and Elasticsearch. Specifically, it logs information about the file system, including which files have changed and when.
- Metricbeat is a lightweight shipper that you can install on your servers to periodically collect metrics from the operating system and from services running on the server. Metricbeat takes the metrics and statistics that it collects and ships them to the output that you specify, such as Elasticsearch or Logstash.

**Additonally the Azure Portal defined the topology seen below:**

![Azure Portal](../../blob/main/Images/topology.svg)


The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.
| Name         | Function           | IP Address    | Operation System |
|--------------|--------------------|---------------|------------------|
| Jump box     | Gateway            | 13.68.227.24  | Linux            |
| Web 1 (DVMA) |                    | 20.25.15.56   | Linux            |
| Web 2 (DVMA) |                    | 20.25.15.56   | Linux            |
| Elk-Server   | Metrics via Kibana | 20.122.12.117 | Linux            |

### Access Policies
The machines on the internal network are not exposed to the public Internet. 

Only the Web virtual machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- 20.25.15.56, which actually maps to a load balancer for the Web VM's configured in the VNet.

Machines within the network can only be accessed by SSH.
- The network was configured to grant access over IP Address and I used my Home laptop's IP address to build the security rules.
  IP Address variable - see Project Challenge below.

### Project Challenge: 
**My internet service provider (ISP) uses dynamic assignment of my home laptop's IP address.  This requires an update to the NSG (Network Security Group) of my virtual network each time I started up the laptop. Using a tool like IP Monkey or IP Chicken to get the current IP address of my laptop, I needed to edit the NSG to reflect this new value.  Otherwise, I would not have access to any of the virtual machines.**

My home laptop was granted access to the ELK VM.  Its IP address varies see: Special Note above. 

### Lesson Learned: 
**When building out the virtual network in Azure, I was unable to load the resources I had created. I got a fetching error msg. (see below)**

![Azure Portal Fetch Error ](../../blob/main/Images/Fetch_Error.png)

**I also had my Anti-Virus Software deny access in the Azure portal with a message about the web address being blacklisted. I was able to update my Anti-virus software with a rule that added an exception to grant access to that web address. This prevented me from getting the fetching error and I was able to access all the resources again in the Azure portal.**

![Virus block](../../blob/main/Images/Virus-block.png)




A summary of the access policies in place can be found in the table below.

| Name         | Publicly Accessible  | Allowed IP Addresses |
|--------------|----------------------|----------------------|
| Jump box     | Yes                  |                      |
| Web 1 (DVMA) | No                   | 20.25.15.56          |
| Web 2 (DVMA) | No                   | 20.25.15.56          |
| Elk-Server   | Yes                  |                      |


### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- Automation gives consistant deployment of the docker containers. This allows for easier management of the entire network. Any modifications or updates to the ansible code files (YAML,etc) is easily done and defined by the existing code.

The playbook implements the following tasks:
- Installation of the docker application using apt install
- Installation of python3-pip using apt install
- Increase virtual memory in the enviroment using sysctl -w vm.max_map_count=262144
- Download and launch the Elk Container using image: sebp/elk:761

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![Elk Stack Container](../../blob/main/Images/Elk_Container_Running.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- Elk server monitors Web 1 (DVMA) and Web 2 (DVMA)

We have installed the following Beats on these machines:
- Filebeats and Metricbeats

Installation of Filebeats using ansible playbook **Filebeat-playbook.yml**

![](../../blob/main/Images/Running%20Filebeat-playbook.yml-edited.png)

Installation of Metricbeats using ansible playbook **Metricbeat-playbook.yml**

![](../../blob/main/Images/Running%20Metricbeat-playbook.yml-edited.png)



### Kibana web pages showing that both Filebeats and Metricbeats are active on Web 1 and Web2 VM


Filebeats Screenshot
![Filebeats Screenshot](../../blob/main/Images/Kibana-Filebeats.png)




Metricbeats Screenshot
![Metricbeats Screenshot](../../blob/main/Images/Kibana-Metricbeats.png)


These Beats allow us to collect the following information from each machine:

- Filebeats in the ELK environment is configured to gather information about the file system logs including which files have changed and when. The logs are organized and sent to Elasticsearch and Logstash.
- Metricbeats in the Elk envirionment is configured to collect metrics associated with the docker containers that have been installed on the Web 1 and Web2 VM. 

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node, the jumpbox in this environment, and follow the steps below:
- Copy the install-elk.yaml file to /etc/ansible directory.
- Update the ansible.cfg file to include the remote_user for the hosts you will be updating.  The /etc/ansible/hosts file also need to update with the IP addresses of the Web VMs.  Additionally you will have independant configuration files to install Filebeats and Metricbeats.(ie. filebeats-config.yml, metricbeats-config.yml)
- Run the playbook, and navigate to kibana web page, **http://<Elk-Server Public IP>:5601/app/kibana** to check that the installation worked as expected.


_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._
