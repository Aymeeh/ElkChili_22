# ElkChili_2022
Elk Stack Project

The files in this repository were used to configure the network depicted below.

![image](https://user-images.githubusercontent.com/92546881/159834666-f2e1878a-238a-43dd-8870-f1d18460ce0f.png)


These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the PLAYBOOK file may be used to install only certain pieces of it, such as Filebeat.

[fileabeat-config.yml](https://github.com/Aymeeh/ElkChili_22/blob/main/Ansible/filebeat-configuration.yml)

[filebeat-playbook.yml](https://github.com/Aymeeh/ElkChili_22/blob/main/Ansible/filebeat-playbook%20(1).yml) 

[metricbeat-config.yml](https://github.com/Aymeeh/ElkChili_22/blob/main/Ansible/metricbeat-configuration.yml)

[metricbeat-playbook.yml](https://github.com/Aymeeh/ElkChili_22/blob/main/Ansible/metricbeat-playbook%20(1).yml)

This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly AVAILABLE, in addition to restricting ACCESS to the network.

- The advantage to having a jump box for this provides one point of egress and ingress, providing a choke point for securing the internal network from malicious external attacks. In addition to this, the jump box provides a entralized location for configuring the internal network(s).

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the network and system configuration.

The configuration details of each machine may be found below.

| Name       | Function                  | IP Address | Operating System     |
|------------|---------------------------|------------|----------------------|
| JumpBox    | Gateway                   | 10.1.0.4   | Linux (Ubuntu 18.04) |
| Web-1      | Traffic Monitor           | 10.1.0.5   | Linux (Ubuntu 18.04) |
| Web-2      | Traffic Monitor           | 10.1.0.6   | Linux (Ubuntu 18.04) |
| ELK Server | Data Analytics Aggregator | 10.2.0.4   | Linux (Ubuntu 18.04) |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the JumpBox (Provisioner) machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- Real World IP address of the "RedAdmin"; SSH (Port 22) from whitelisted IP address.

Machines within the network can only be accessed by JumpBox (Provisioner), and it's corresponding Ansible container at 10.1.0.4.

A summary of the access policies in place can be found in the table below.
| Name      | Publicly Accessible | Allowed IP Address                                |
|-----------|---------------------|---------------------------------------------------|
| JumpBox   | Yes                 | SSH from whitelisted IP addresses through Port 22 |
| Web-1     | No                  | None                                              |
| Web-2     | No                  | None                                              |
| ElkServer | Yes                 | SSH from whitelisted IP addresses through Port 22 |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because it allows scalable deployment and the ability to spin up multiple VMs with the same configuration, as well as eliminating the manual configuration entry errors.

The playbook implements the following tasks:
- Install DOCKER.IO
- Install PIP3
- Install DOCKER PYTHON
- Configure sysctl modules to: use more memory, download and launch a Docker Elk container, and start docker on boot

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.
````sysadmin@ElkServer:~$ sudo docker ps

    CONTAINER ID          IMAGE                COMMAND                       CREATED          STATUS       
    339979bbe0e9          sebp/elk:761         "/usr/local/bin/star..."      6 days ago       Up 2 hours   
    
    PORTS                                                                                         NAMES
    0.0.0.0:80->80/tcp, 0.0.0.0:5044->5044/tc, 0.0.0.0:5601->5601/tcp, 0.0.0.0:9200->9200/tcp     elk
````

### Target Machines & Beats

This ELK server is configured to monitor the following machines:
- Web-1: 10.1.0.5
- Web-2: 10.1.0.6

We have installed the following Beats on these machines:
- Filebeat: version 7.6.2
- Metricbeat: version 7.6.2

These Beats allow us to collect the following information from each machine:
- Filebeat: collects data about the file system and services, forwarding and centralizing both logs and files.
- Metricbeat: collects machine metrics, such as uptime, along with sending system and service statistics.

### Using the Playbook

In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Create the configuration files with the names: filebeat-config.yml and metricbeat-config.yml, placing them in the /etc/ansible/FILES
  directory on your Ansible container within the JumpBox provisioner.
- Create both the filebeat-playbook.yml and metricbeat-playbook.yml. Each should be placed into the /etc/ansible/ROLES directory in your
  Ansible container in the JumpBox provisioner.
- To ensure the playbooks properly send logs to the ELK VM in which the data and logs will be collected, you will need to update the filebeat and
  metricbeat configuration files to point to the private IP address of your ELK server. (Lines #1106 and #1806)

- Update the IP address on this line with the public IP address of your ELK machine:


````output.elasticsearch:
    hosts: ["10.1.0.4:9200"]
    username: "elastic"
    password: "changeme"
````

- Update this IP address with the same:
 
````setup.kiabaa:
    host: ["10.1.0.4:5601"]
```` 
-/etc/ansible/hosts

You will then need to update the  `/etc/ansible/hosts` file with the added `elk` server:

 ````[webservers]
     10.1.0.5 ansible_python_interpreter=/usr/bin/python3
     10.1.0.6 ansible_python_interpreter=/usr/bin/python3

     [elk]
     10.2.0.4 ansible_python_interpreter=/usr/bin/python3
````
 
- After you have completed the above tasks and configured the Ansible container appropriately, the following commands will need to be run in order to ensure the playbook(s) were configured correctly:

 ````
     ansible-playbook filebeat-playbook.yml
 
     ansible-playbook metricbeat-playbook.yml
 ````

- If the deplayment ran correctly with no fatal errors or otherwise ntoes issues, transition to the web address of: `http://<current ELK public IP address>:5601/app/kibana/home`. Once executed, this address should open to the Kibana landing page related to your ELK VM where you can view and filter any logs and/or data compiled.

