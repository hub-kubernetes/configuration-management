# Puppet vs Ansible Demo 

### Pre-requisite 

Three virtual machines - 

* master - Ansible and Puppet master 

* slave - Puppet Slave 

* ansibleslave - Ansible node 

* Puppet server and agent installed 

* Ansible inventory setup and ssh setup between master and ansibleslave 


## Create a directory

### Create a directory with puppet - 

On the master 

```
vi /etc/puppetlabs/code/environments/production/manifests/site.pp
```

Add the below lines to the file - 

```
node 'YOUR_PUPPET_SLAVE_NAME' { # Applies only to mentioned node. If nothing mentioned, applies to all.
     file { '/tmp/puppetdir': # Resource type file
             ensure => 'directory', # Create as a diectory
             owner => 'root', # Ownership
             group => 'root', # Group Name
             mode => '0755', # Directory permissions
          }
}
```

On the agent server run - 

```
/opt/puppetlabs/bin/puppet agent --test
```


### Create a directory with ansible - 

On master - 

```
ansible YOUR_ANSIBLE_SLAVE_NAME  -m file -a "path=/tmp/ansibledir state=directory"
```

---

## Install packages

### Install mysql with puppet 

```
vi /etc/puppetlabs/code/environments/production/manifests/site.pp
```

Add the below lines within the scope of your slave node - 

```
 package { 'mysql-client' :
    ensure => present,
  }

```

On the agent server run - 

```
/opt/puppetlabs/bin/puppet agent --test
```

### Install mysql with ansible 


On master 

```
ansible database -m apt -a "name=mysql-client state=latest"
```


### Install docker with puppet

```
vi /etc/puppetlabs/code/environments/production/manifests/site.pp
```

Add the below lines - 


```
 package { 'docker.io' :
    ensure => present,
  }

```

On the agent server run - 

```
/opt/puppetlabs/bin/puppet agent --test
```

### Install docker with ansible 

On master node - 

```
ansible database -m apt -a "name=docker.io state=latest"
```


## For assignment - Install nginx package using puppet and ansible


---

## Working with Puppet Modules

> Modules are self-contained bundles of code and data. You can download pre-built modules from the Puppet Forge or you can write your own modules. You create modules with set of code required to set up your infrastructure

### Puppet module to add user 

On Puppet master - 

```
cd /etc/puppetlabs/code/environments/production/modules/
mkdir useraccount
cd useraccount
mkdir {examples,files,manifests,templates}

```


What are these directories - 

* manifests -	The Puppet code which powers the module
* files -	Static files to be copied to managed nodes
* templates - 	Template files to be copied to managed nodes that can e customized with variables
* examples -	Example code which shows how to use the module


> Any file which contains Puppet code is called a manifest, and each manifest file ends in .pp. When located inside a module, a manifest should only define one class. If a module’s manifests directory has an init.pp file, the class definition it contains is considered the main class for the module. The class definition inside init.pp should have the same name as the module.


```
cd manifests
vi init.pp
```

Add the below lines - 

```
class useraccount {

  user { 'testuser':
    ensure      => present,
    home        => '/home/testuser',
    shell       => '/bin/bash',
    managehome  => true,
    gid         => 'testuser',
  }

}
```
Save the file

To create the group - now create a new file - groups.pp 

```
vi groups.pp
```
Add the below lines 

```

class useraccount::groups {

  group { 'testuser':
    ensure  => present,
  }

}

```

Now reference the groups class inside init.pp

```
vi init.pp
```

Add the below lines just after **class useraccount**
```
  include useraccount::groups

```

Your final file should look like below - 

```
class useraccount {
  include useraccount::groups

  user { 'testuser':
    ensure      => present,
    home        => '/home/testuser',
    shell       => '/bin/bash',
    managehome  => true,
    gid         => 'testuser',
  }

}

```

Perform a test run of your files - 

```
cd ../examples
vi init.pp
```


Add the below line - 

```
include useraccount
```

Run the below command to perform a dry run - 

```
/opt/puppetlabs/bin/puppet apply --noop init.pp

```

Execute the below command to apply your changes - 

```
/opt/puppetlabs/bin/puppet apply init.pp
```

Apply changes to agent nodes now - 

dd the include statement to site.pp at /etc/puppetlabs/code/environments/production/manifests

```
cd ../../../manifests/
vi site.pp
```

```
node default {

}

node 'YOUR_MASTER_NAME' {
  # ...
}

node 'YOUR_SLAVE_NAME' {

  include useraccount
}
```

On **Agent** machine run the below to avoid wait time for polling 

```
/opt/puppetlabs/bin/puppet agent -t

```

---

## Working with Ansible playbook 

> Ansible Playbook is a set of instructions that you send to run on a single or group of server hosts. It represents the ansible-provisioning, where the automation is defined as tasks, and all jobs like installing packages, editing files, will be done by ansible modules.

> The Ansible Playbook contains some basic configuration, including hosts and user information of the provision servers, a task list that will be implemented to the provision servers, template and custom configurations, and a group of variables part of templates and tasks.

### Ansible playbook to add a user - 

Create a file user.yaml on master - 

```
vi user.yaml
```

Add the below lines - 

```
---
- name: Add User
  hosts: slave
  gather_facts: true

  tasks:

  - name: Create a login user
    user:
      name: ansibleuser
      state: present
      shell: /bin/bash       # Defaults to /bin/bash
      system: no             # Defaults to no
      createhome: yes        # Defaults to yes
      home: /home/ansibleuser  # Defaults to /home/<username>


```

From master execute - 

```
ansible-playbook user.yaml
```



















