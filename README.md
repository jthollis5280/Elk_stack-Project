# Elk_stack-Project
Created through Microsoft Azure by Jeff Hollis 2021
## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

<img width="499" alt="overview" src="https://user-images.githubusercontent.com/87458325/146653717-dacecb42-c068-4425-ba65-5b629168ed7e.PNG">
https://drive.google.com/file/d/1Q_grVgkRYz976n7f5iQpL8mQ_w_Cu43w/view?usp=sharing


These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the **Yaml and config** files may be used to install only certain pieces of it, such as Filebeat.

  - _TODO: Enter the playbook file._

This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly **available**, in addition to restricting **traffic** to the network.
 
- What aspect of security do load balancers protect? Load balancers help mitigate Dos attacks, which is a common problem with websites. The load balance recieves any traffic that comes in and distributes it across multiple servers. In our case Web 1 and Web 2. Configurations of health probes helps ensure the functionality and availability to keep sites stable.
- What is the advantage of a jump box? The advantage of a JBox is that we can configure it to restrict IP addresses and allow our Azure VMs to not be directly exposed to the internet through a public IP. It acts as a Front door security guard to the VMs and only lets in IP address that we configure within it's rules set. 

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the **network** and **system logs**.
-What does Filebeat watch for? Filebeat collects log events.
-What does Metricbeat record? Metricbeat takes statistics and metric.

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name     | Function | IP Address                         |     Operating System |
|----------|----------|------------------------------------|----------------------|
| Jump Box | Gateway  | 10.0.0.4 / 20.121.26.104           |     Linux            |
| Web1     |   Ubuntu | 10.0.0.5 / 23.96.60.43             |     Linux            |
| Web2     |   Ubuntu | 10.0.0.6 / 23.96.60.43             |     Linux            |
| ELK_VM   |   Ubuntu | 10.1.0.4 / 52.159.72.172           |     Linux            |


### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the **JBOX** machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
-SSH azadmin@20.121.26.104

Machines within the network can only be accessed by **SSH**.
Which machine did you allow to access your ELK VM? Jump box SSH azadmin@10.1.0.4 (assuming public key is correct on Jbox) through port 22
What was its IP address? private is 10.1.0.4; public 52.159.72.172:5601

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses         |
|----------|---------------------|------------------------------|
| Jump Box |  Yes                | ssh azadmin@20.121.26.104    |
| Web 1    |  No                 | ssh azadmin@10.0.0.5         |
| Web 2    |  No                 | ssh azadmin@10.0.0.6         |
| ELK      |  No                 | ssh azadmin@10.1.0.4         |
| ELK      |  No                 | Http 52.159.72.172:5601      |


### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because **it speeds up the process of deploying several task in a one time configuration**.
What is the main advantage of automating configuration with Ansible? **The main advantage of automating through ansible is that you can do serveral machines with one YAML playbook**

The playbook implements the following tasks:
 name: Configure Elk VM with Docker
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
        value: 262144
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
          -  5601:5601
          -  9200:9200
          -  5044:5044

      # Use systemd module
    - name: Enable service docker on boot
      systemd:
        name: docker
        enabled: yes



The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.
![image](https://user-images.githubusercontent.com/87458325/147624217-5456e5dd-ea71-4918-b002-1a9c8da201f3.png)


### Target Machines & Beats
This ELK server is configured to monitor the following machines:
  List the IP addresses of the machines you are monitoring
    * Web 1 : 10.0.0.5
    * Web 2 : 10.0.0.6
    * ELK_VM : 10.1.0.4

We have installed the following Beats on these machines:
 Specify which Beats you successfully installed
    * Filebeat
    * Metricbeat
    <img width="934" alt="part 4 verifying install" src="https://user-images.githubusercontent.com/87458325/147625491-690d7e7e-5770-4428-b19e-46a262187a5a.PNG">

    <img width="897" alt="part 5 metrics data2" src="https://user-images.githubusercontent.com/87458325/147625495-d14c51a2-b677-4adb-8e54-4a73a7ea99c9.PNG">


These Beats allow us to collect the following information from each machine:
  In 1-2 sentences, explain what kind of data each beat collects, and provide 1 example of what you expect to see. E.g., `Winlogbeat` collects Windows logs, which we use to track user logon events, etc.
  Filebeat: Collects log data from several sources like apache, elasticsearch AWS Cloudwatch.
  ![image](https://user-images.githubusercontent.com/87458325/147626199-22f8eb06-3e77-44bf-a117-afd137b6914d.png)

  Metricbeat:Collects metric data used to monitor VM's examples include CPU usage, DiskIO, Memory, and Network IO ECS.
  <img width="932" alt="part 5 metrics data" src="https://user-images.githubusercontent.com/87458325/147626312-16cf2e09-7060-4b66-9e2f-8b1ca63b369f.PNG">
<img width="897" alt="part 5 metrics data2" src="https://user-images.githubusercontent.com/87458325/147626314-4793d5ed-dda7-4a7a-9bc8-6a0053894e96.PNG">



### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the **YAML** file to **Ansible folder**.
- Update the **Config file** to include the **correct ports and azadmin(USER)**.
- Run the playbook, and navigate to **Kibana ** to check that the installation worked as expected.
- ![image](https://user-images.githubusercontent.com/87458325/147626905-f845f2e9-5679-494b-b19e-ba81963bbc67.png)


