# Ansible Configuration Management
Manually updating a configuration-managed host and set their changes could to be immutable. Which is a state that cannot be modified after it is created, thus overriding the expected state and preventing the CM system from applying future changes. Ansible CM operations are designed to be applied repeatedly without causing problems (idempotence).
let's start with the files needed and their common configurations.

## inventory file
This file is where the hostnames or ip addresses you wanted to manage.
```
[dbservers]
192.168.122.161
192.168.122.214

[fileservers]
192.168.122.95
192.168.122.81
```
## ansible.cfg

This file is where you define your inventory file, private_key_file, remote_user.
```
[defaults]
inventory = inventory
private_key_file = ~/.ssh/target_servers
remote_user = devop
```

## changes.yml
Desired configuration changes.
```
---

 - hosts: all
   become: true
   pre_tasks:

   - name: install updates (CentOS)
     tags: always
     dnf:
       update_only: yes
       update_cache: yes
     when: ansible_distribution == "CentOS"

   - name: install updates (Ubuntu)
     tags: always
     apt:
       upgrade: dist
       update_cache: yes
     when: ansible_distribution == "Ubuntu"
```

[ Ansible Reference ](https://docs.ansible.com/ansible/latest/getting_started/index.html )