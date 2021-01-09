# ELKStackProject
## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.
### Link to Diagram:
https://github.com/zainahazam4/ELKStackProject/blob/main/diagram/ZA_Cloud_Network_Diagram.png

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the .yml files may be used to install only certain pieces of it, such as Filebeat.

### Link to Elk Install YAML
https://github.com/zainahazam4/ELKStackProject/blob/main/AnsibleYAML/install-elk%20.yml

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting inbound access to the network.
- What aspect of security do load balancers protect? Load balancers defend against distributed denial-of-service (DDoS) attacks. 
- What is the advantage of a jump box? The advantage of a jumpbox is to give a user access from a single node which can be secured and monitored (i.e. fanning in). The jump-box is also hidden from the public allowing the admin to perform tasks on the network. 

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the logs and system traffic.
- What does Filebeat watch for? Filebeat monitors log files or specified locations, collects log events and forwards them to Elasticsearch or Logstash for indexing. 
- What does Metricbeat record? Metribeat collects and logs the system metrics of your network.

The configuration details of each machine may be found below.

| Name     | Function | IP Address              | Operating System |
|----------|----------|-------------------------|------------------|
| Jump-Box | Gateway  | 52.152.146.183/10.0.0.1 | Linux            |
| Web-1    |Web Server| 10.0.0.5                | Linux            |
| Web-2    |Web Server| 10.0.0.6                | Linux            |
| Web-3    |Web Server| 10.0.0.7                | Linux            |
| Elk-VM   |Monitoring| 13.91.17.23/10.1.0.4    | Linux            |


### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump Box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
### Refer to chart below

Machines within the network can only be accessed by each other. [ Jump Box can SSH into the Web Servers and Elk Servers. Web Servers sent log information to the Elk Server ]
- Jump Box VM: VNET IP 10.0.0.4 

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump-Box | Yes                 | Admin Public IP      |
|  Web-1   | No                  | 10.0.0.1/24          |
|  Web-2   | No                  | 10.0.0.1/24          |
|  Web-3   | No                  | 10.0.0.1/24          |
|  Elk-VM  | Yes                 | 10.0.0.1/24          |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- it allows us to install, update, add web servers to our network using the same playbooks

The playbook implements the following tasks:
- It starts by installing docker on all network machines so that it can receive and install containers
- The Ansible is installed on the Jump-Box-Provisioner virtual machine(VM) to distribute containers to other VMs on the network. 
- Ansible playbooks are used to install the ELK stack container on the Elk server and a 'Beats' containers on the Web Servers. 

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

### Link to Docker ps output
https://github.com/zainahazam4/ELKStackProject/blob/main/images/dockerpsscreenshot.png

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- Web-1 - 10.0.0.5
- Web-2 - 10.0.0.6
- Web-3 - 10.0.0.7

We have installed the following Beats on these machines:
- Filebeat 
- Metricbeat

These Beats allow us to collect the following information from each machine:
-  We are using filebeat to monitor web log data. It detects changes to the file system. 
-  We are using metricbeat to detect SSH Login attempts, failed sudo escalations and CPU/RAM statistics. It detects changes in system metrics. 

### Using the Playbook 
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node (jumpbox) and follow the steps below:
- Copy the playbook file to /etc/ansible directory.
- Update the hosts file to include webservers and the elk internal ip of elk-vm. 
- Run the playbook and SSH into the elk-vm. Run the command 'docker ps' to check that the installation worked as expected.

Answers to the following questions
Which file is the playbook? 
- install-elk.yml
Where do you copy it?
- /etc/ansible/install-elk.yml
- copy:
```
---
- name: Configure Elk VM with Docker
  hosts: elkservers
  remote_user: elk
  become: true
  tasks:
    # Use apt module
    - name: Install docker.io
      apt:
        update_cache: yes
        name: docker.io
        state: present

      # Use apt module
    - name: Install pip3
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

      # Use pip module
    - name: Install Docker python module
      pip:
        name: docker
        state: present

      # Use sysctl module
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
        reload: yes

      # Use docker_container module
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        published_ports:
          - 5601:5601
          - 9200:9200
          - 5044:5044
```
Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?
- We need to edit the /etc/ansible/host file to add webserver or elk server IP addresses

Which URL do you navigate to in order to check that the ELK server is running?
### Link to Kibana
http://13.91.17.23:5601/app/kibana#/home
