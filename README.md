# Substrate Private Network Ansible Repo

This Repo contains different ansible roles for different Purpose

You can learn about substrate private network setup at https://substrate.dev/docs/en/tutorials/start-a-private-network-with-substrate

# Ansible Roles

`substrate-role`: This Role is used to deploy a private substrate or polkadot network of two nodes with a custom genesis file.
`explorer-ui`: This Role is used to deploy the polkadot Explorer UI.
`monitoring`:This Role is used to setup and configure the ELK System.
`metrics`: This role is used to configure & push the monitoring metrics such as system, nginx, etc on the ELK system.

# Prerequitics:

* The Roles are tested on `ubuntu 16.04`
* Ansible version is `2.8.6`

Instructions to setup the whole system:

Setup Ansible Server:
```
Launch an ec2 server in aws 
Install Ansible
Modify the hosts file and create multiple hosts as per your requirement. 

Sample host file:
[explorer-ui]
54.162.7.156
[monitoring]
54.84.164.236
[substrate-node]
54.224.47.244
3.90.115.34 
```
```
Change the ansible.cfg according to the requirement.
   A. host_key_checking = False
   B. remote_user = ubuntu
   C. private_key_file = /home/ubuntu/demo.pem
```
Setup the Substrate Nodes:
1. Launch 2 aws ec2 Instances ( Please use atleast t2.medium )
2. Add the public IPs of both the instances in the host file.
3. Add both these also in the substrate-node.yml file to make one node as validator node ( where the custom genesis file is generated ) and other node called as additional node.

Sample substrate-node.yml file:
---
- hosts: substrate-node
  vars:
    consensus_authorities: []
    validator_node_id: ""
    validator_node: 54.224.47.244
    additional_node: 3.90.115.34
  roles:
    - substrate-node

4. After this, Please run the playbook as --> ansible-playbook substrate-node.yml 
5. This is configure both the nodes and create the network. You can check by log in to the instances.

Setup the Explorer UI server:
1. Launch an aws EC2 instance ( atleast t2.medium )
2. Add the public IP of the instance in the host file.
3. Run the ansible playbook as --> ansible-playbook explorer-ui.yml
4. It will Setup the explorer-ui and setup nginx also. You can open on http://PUBLIC_IP_OF_INSTANCE.

Setup the ELK server:
1. Launch an aws EC2 instance ( atleast t2.medium )
2. Add the public IP of the instance in the host file.
3. Run the ansible playbook as --> ansible-playbook monitoring.yml
4. It will Setup the elasticsearch, kibana, nginx. You can open the kibana dashboard on http://PUBLIC_IP_OF_INSTANCE.

Setup and configure the Metrics to push on ELK Server:
1. You can use this role with any other playbooks like with explorer-ui , substrate-node etc. This role is used to setup the metricbeat and heartbeat on the required servers.
2. You need to change the default variable values like elasticsearch and kibana URL so that beats can push the metrics on the ELK or monitoring Server.
3. For adding defined metrics, Please change the  default files for the services
