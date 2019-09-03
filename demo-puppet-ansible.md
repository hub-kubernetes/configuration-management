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























