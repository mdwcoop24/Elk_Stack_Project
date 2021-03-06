# Elk_Stack_Project
## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![alt text](https://github.com/mdwcoop24/Elk_Stack_Project/blob/main/Diagrams/Azure-VN.png "Azure Virtual Network")

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the YML file may be used to install only certain pieces of it, such as Filebeat.

  - [Ansible Playbook](https://github.com/mdwcoop24/Elk_Stack_Project/tree/main/Ansible/Pentest.yml)
  - [Elk Stack Playbook](https://github.com/mdwcoop24/Elk_Stack_Project/tree/main/Ansible/Install-elk.yml)
  - [Filebeat Playbook](https://github.com/mdwcoop24/Elk_Stack_Project/tree/main/Ansible/Filebeat.yml)
  - [Metricbeat Playbook](https://github.com/mdwcoop24/Elk_Stack_Project/tree/main/Ansible/Metricbeat.yml)

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly *available*, in addition to restricting *access* to the network.
- Load balancers do not just distribute network or application traffic across a number of servers, they also protect against Denial of Service (DDoS) attacks by analyzing that traffic. 
- A Jump box is a hardened device that allows access and easy administration of other devices and servers maintained by Administrators. This also helps with completeing administative tasks over several devices at once.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the *data* and system *logs*.
- Filebeat watches for log files and locations that you specify, and will collect and index them into either Elasticsearch or Logstash. Such as Apache, Azure tools, or MySQL logs.
- Metricbeat monitors the servers by collecting metrics from the system and the services running, and then sends the output to either Elasticsearch or Logstash. 

The configuration details of each machine may be found below.


| Name     | Function    | IP Address | Operating System |
|----------|-------------|------------|------------------|
| Jump Box | Gateway     | 10.0.0.4   | Linux Ubuntu     |
| Web-1    | WebServer   | 10.0.0.5   | Linux Ubuntu     |
| Web-2    | WebServer   | 10.0.0.6   | Linux Ubumtu     |
| ElkVM    |Log Server   | 10.1.0.4   | Linux Ubuntu     |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump Box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- Workstation public IP address 107.126.59.63  

Machines within the network can only be accessed by the *Jump Box*.
- The ElkVM can only be accessed by the Jump Box and the workstaion machine. This is done by using the ElkVM IP 10.1.0.4 and doing a ssh through port 22, and using the workstation public IP via TCP port 5601.

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes                 | 20.58.178.26 (public)|
| ElkVM    | Yes (TCP Port 5601) | 20.37.7.13   (public)|
| Web-1    | No                  | 10.0.0.5             |
| Web-2    | No                  | 10.0.0.6             |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because:
- Ansible tasks will execute one line of the playbook at a time. If there is a error in the playbook, ansible will stop at that error in turn making it easier to troubleshoot. 

The playbook implements the following tasks:
- Install the latest version of docker.io and python3-pip
- Increase virtual memory
- Download and launch a docker elk container and establishes the restart policy of always
- Established ports that ELK runs on: 5601, 9200, 5044

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![alt text](https://github.com/mdwcoop24/Elk_Stack_Project/blob/main/Images/Docker.PNG)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- Web-1 10.0.0.5
- Web-2 10.0.0.6

We have installed the following Beats on these machines:
- Filebeat
- Metricbeat

These Beats allow us to collect the following information from each machine:
- Filebeat collects log files such as audit logs, server logs, and Java Garbage Collector (GC) logs. 
- Metricbeat collects the data from the operating system (i.e. CPU and memory statistics) and from services running on the system such as Apache and HAProxy.

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the filebeat configuration file to /etc/ansible/files.
- Update the filebeat configuration file to include the private IP of the Elk machine in lines 1106 and 1806.
- Run the playbook, and navigate to http://Elk-server-ip:5601/app/kibana to check that the installation worked as expected.

 _Which file is the playbook? Where do you copy it?_
  - filebeat-playbook.yml 
  - Copied the playbook to /etc/ansible/roles

_Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
  - You will update the /etc/ansible/hosts file to include the IP addresses of the machines you will run the playbook on.   Once this is done, you will specify which "hosts" group to run the playbook on within the playbook itself. If Elk is listed as host, it will download to the ELK server whereas if webservers is listed as host, it will install on the DVWR machines.  

Which URL do you navigate to in order to check that the ELK server is running?
  - http://Elk-server-ip:5601/app/kibana

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._
- SSH to your Jumpbox from your workstation
- Start your Ansible container and attach 
  - **sudo docker start (container name)**
  - **sudo docker attach (container name)**
- You will then need to download the template filebeat-config.yml to your /etc/ansible/files
  - Go to [Filebeat Config](https://github.com/mdwcoop24/Elk_Stack_Project/blob/main/Ansible/Filebeat-config.yml) copy the entire configuration file. Then go to /etc/ansible/files and type **nano filebeat-config.yml** and paste it. 
  - While in the filebeat configuration, edit the file to reflect the Elk IP and port.
    - Edit line **1106**. Replace the IP with the Elk IP and the port to **9200**
    - Edit line **1806**. Replace the IP with the Elk IP and the port to **5601**
- When finished, navigate to the /etc/ansible/roles and create your filebeat playbook
  - You can copy and paste it from [Filebeat Playbook](https://github.com/mdwcoop24/Elk_Stack_Project/tree/main/Ansible/Filebeat.yml) 
  - Run the playbook **ansible-playbook filebeat-playbook.yml**
- Navigate to http://Elk-server-ip:5601/app/kibanna/home
  - Click **Module Status** then click on **Check Data**
  
The result should look like this : 
![alt text](https://github.com/mdwcoop24/Elk_Stack_Project/blob/main/Images/Filebeat.PNG "Filebeat Data")
