## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

(https://github.com/Nightwing99ktm/ELK-Stack-Project/blob/master/images/Completed%20ELK-Stack%20Diagram.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the YAML file may be used to install only certain pieces of it, such as Filebeat.

 [install-elk.yml](https://raw.githubusercontent.com/Nightwing99ktm/forkthisprojectsite/master/install-elk.yml)
 
 ```
 ---
  - name: Configure Elk VM with Docker
    hosts: elk
    remote_user: azadmin
    become: true
    tasks:
      # Use apt module
      - name: Install docker.io
        apt:
          update_cache: yes
          force_apt_get: yes
          name: docker.io
          state: present

      # Use apt module
      - name: Install python3-pip
        apt:
          force_apt_get: yes
          name: python3-pip
          state: present

      # Use pip module (It will default to pip3)
      - name: Install Docker module
        pip:
          name: docker
          state: present

      # Use command module
      - name: Increase virtual memory
        command: sysctl -w vm.max_map_count=262144

      # Use sysctl module
      - name: Use more memory
        sysctl:
          name: vm.max_map_count
          value: '262144'
          state: present
          reload: yes

      # Use docker_container module
      - name: download and launch a docker elk container
        docker_container:
          name: elk
          image: sebp/elk:761
          state: started
	  restart_policy: always
        # Please list the ports that ELK runs on
          published_ports:
            - 5601:5601
            - 9200:9200
            - 5044:5044

      # Enable Docker Service
      - name: Enable docker service
        systemd:
          name: docker
          enabled: yes
```

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly accesible, in addition to restricting inbound traffic to the network.
- Load balancers allow traffic to be distributed from clients across multiple servers, thus enhancing the user experince by providing additional security, performance, and resilience to attack. 

What is the advantage of a jump box?
- A jump box can be configured to be a secure computer that all administrators connect to before launching any administrative tasks or as an origination point that can connect to other servers or untrusted environments. 

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the jumpbox and system network.

What does Filebeat watch for?
- Filebeat watches for changes made to log files or locations and collects log events. 

What does Metricbeat record?
- Metricbeat records metrics from the operating system and from services running on the server.

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.0.0.4   | Ubuntu Linux 18.04|
| Web-1    | Webserver| 10.0.0.5   | Ubuntu Linux 18.04|
| Web-2    | Webserver| 10.0.0.6   | Ubuntu Linux 18.04|
| Web-3    | Webserver| 10.0.0.8   | Ubuntu Linux 18.04|
| ELK-Server| Monitoring | 10.1.0.4 | Ubuntu Linux 18.04|

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the jump box provisioner machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- 136.35.37.0/24 via SSH to the Jump-Box Provisioner Public IP of 40.87.120.109.

Machines within the network can only be accessed by the jump-box provisioner.
- The Jump-box provisioner (Public IP: 40.87.120.109; Private IP: 10.0.0.4)
A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes                 | 136.35.37.0/24       | 
| Web 1-3  | No                  | 10.0.0.4             | 
| ELK VM   | No                  | 10.0.0.4             | 
 

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- The YAML playbooks can be used to get the same result everytime.
- The configuration is completely isolated from the public minus the specifically allowed access via the IP range. 

The playbook implements the following tasks:
- Installs Docker
- Installs pip3
- Installs Docker python module
- Increase virtual memory
- Download and launch docker

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![Docker PS Output](images/ELK docker ps.jpg)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
| Name     | IP Addresses |
|----------|--------------|
| Web-1    | 10.0.0.5     | 
| Web-2    | 10.0.0.6     | 
| Web-3    | 10.0.0.8     | 
| ELK VM   | 10.1.0.4     |
We have installed the following Beats on these machines:
- Filebeats
- Microbeats

These Beats allow us to collect the following information from each machine:
- Filebeat collects data about the file system and moitors log changes that are made. Metricbeat collects machine metrics and performance statistics such as uptime.

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the hosts file to /etc/ansible.
- Update the hosts file to include a group notated by 
- Run the playbook, and navigate to ____ to check that the installation worked as expected.

_TODO: Answer the following questions to fill in the blanks:_
- _Which file is the playbook? Where do you copy it?_
- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
- _Which URL do you navigate to in order to check that the ELK server is running?

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._
