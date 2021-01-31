## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![ELK-Stack Diagram](https://github.com/Nightwing99ktm/ELK-Stack-Project/blob/master/images/Completed%20ELK-Stack%20Diagram.png)

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

![Docker PS Output](images/ELK_docker_ps.jpg)

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
- Copy the following YAML files to the Ansible control node: [Install-ELK](https://raw.githubusercontent.com/Nightwing99ktm/ELK-Stack-Project/master/install-elk.yml) | [Install-Filebeat](https://raw.githubusercontent.com/Nightwing99ktm/ELK-Stack-Project/master/playbooks/filebeat-playbook.yml) | [Install-Metricbeat](https://raw.githubusercontent.com/Nightwing99ktm/ELK-Stack-Project/master/playbooks/metricbeat-playbook.yml)

```
$ cd /etc/ansible
$ mkdir files

# Clone Git Repository
$ git clone https://github.com/Nightwing99ktm/ELK-Stack-Project

# Move yml files into '/etc/ansible/files'
$ cp Elk-Stack-Project/playbooks/* files/
```

- Update the hosts file to include the webservers and elk groups. Additionally, configure the IP address to match your machines.

```
$ cd /etc/ansible
$ nano hosts

# Ex 2: A collections of hosts belonging to the 'webservers' group
[webservers]
10.0.0.5 ansible_python_interpreter=/usr/bin/python3
10.0.0.6 ansible_python_interpreter=/usr/bin/python3
10.0.0.8 ansible_python_interpreter=/usr/bin/python3

[elk]
10.1.0.4 ansible_python_interpreter=/usr/bin/python3
```

- Run the instal-elk.yml file, and navigate to *http://[ELK-Public-IP]:5601/app/kibana#/home* to check that the installation worked as expected. (Ensure a Network Security Group rule exists allowing TCP traffic over Port 5601)

```
$ cd /etc/ansible/files
$ ansible-playbook install-elk.yml
```

### Installing Filebeat
- Navigate to your ELK server homepage.
	- Click on Add Log Data
	- Choose System Logs.
	- Click on the DEB tab under Getting Started to view the correct Linux Filebeat installation instructions.

- Within your Ansible container run the following command:

```
$ curl curl https://gist.githubusercontent.com/slape/5cc350109583af6cbe577bbcc0710c93/raw/eca603b72586fbe148c11f9c87bf96a63cb25760/Filebeat > /etc/ansible/files/filebeat-config.yml

```
-Open up the filebeat-config.yml to edit IP addresses

```
$ cd /etc/ansible/files
$ nano filebeat-config.yml
```

- Navigate to line 1106 and replace the IP address with the IP address of your ELK machine.

```
output.elasticsearch:
hosts: ["10.1.0.4:9200"]
username: "elastic"
passwork: "changeme"
```

- Scroll to line 1806 and replace the IP address with the IP address of your ELK machine.

```
setup.kibana:
host: "10.1.0.4:5601"
```

- Within your Ansible container run the following commands to install Filebeat.

```
$ cd /etc/ansible/files
$ ansible-playbook filebeat-playbook.yml
```

- Finally to confirm that the ELK stack is receiving logs, navigate back to the Filebeat installation page on the ELK server page, scroll to Step 5: Module Status and click Check Data.

### Install Metricbeat
- Navigate to your ELK server homepage.
	- Click on Add Metric Data
	- Click Docker Metrics
	- Click on the DEB tab under Getting Started for the corredt Linux instructions.
	
- Within your Ansible container run the following command:

```
curl https://gist.githubusercontent.com/slape/58541585cc1886d2e26cd8be557ce04c/raw/0ce2c7e744c54513616966affb5e9d96f5e12f73/metricbeat > /etc/ansible/files/metricbeat-config.yml
```

- Open up the metricbeat-config.yml to edit the IP addresses

```
$ cd /etc/ansible/files
$ nano metricbeat-config.yml
```

- Navigate to line 62 and replace the IP address with the IP address of your ELK machine

```
# This requires a Kibana endpoint configuration.
setup.kibana:
host: "10.1.0.4:5601"
```

- Navigate to line 95 and replace the IP address with the Ip address of your ELK machine

```
output.elasticsearch:
# Array of hosts to connect to.
hosts: ["10.1.0.4:9200"]
username: "elastic"
password: "changeme"
```

- Within your Ansible container run the following commands to install Metricbeat.

```
$ cd /etc/ansible/files
$ ansible-playbook metricbeat-playbook.yml
```

- Finally to confirm that the playbook worked as expected, navigate back to the Metricbeat installation page on the ELK server page, scroll to Step 5: Module Status and click Check Data.
