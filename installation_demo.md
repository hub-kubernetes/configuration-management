# Puppet server - agent installation 


## Steps for master 

* Download the package for puppet master 

```
wget https://apt.puppetlabs.com/puppet6-release-bionic.deb
sudo dpkg -i puppet6-release-bionic.deb
sudo apt update

```

* Install puppet master 

```
apt-get update && apt install -y puppetserver

```

* Verify puppet master policy & version 

```
apt policy puppetserver
```



* Configure parameters for puppet master 

```
# Change java process memory allocation

sed -i 's/2g/512m/g' /etc/default/puppetserver

```


* Add configuration to puppetserver 

```
echo "dns_alt_names = YOUR_MASTER_NAME" >>/etc/puppetlabs/puppet/puppet.conf

vi /etc/puppetlabs/puppet/puppet.conf
```

Add the below lines - 

```
[main]
certname = YOUR_MASTER_NAME
server = YOUR_MASTER_NAME 
environment = production
runinterval = 15m
```

* Generate Root CA for server 

```
 /opt/puppetlabs/bin/puppetserver ca setup
```


* Restart and enable puppetserver 

```
sudo systemctl start puppetserver
sudo systemctl enable puppetserver

```

## Steps for agent 

* Download package for puppet 

```
wget https://apt.puppetlabs.com/puppet6-release-bionic.deb
sudo dpkg -i puppet6-release-bionic.deb
sudo apt update

```

* Install puppet 

```
apt-get update && apt install -y puppet-agent
```

* Configure puppet agent 

```
vi /etc/puppetlabs/puppet/puppet.conf
```

Add the below lines - 

```
[main]
certname = YOUR_SLAVE_NAME
server = YOUR_MASTER_NAME
environment = production
runinterval = 15m
```

Start puppet agent 

```
/opt/puppetlabs/bin/puppet resource service puppet ensure=running enable=true

systemctl restart puppet
```

## Go back to master 

* Add agent 

```
/opt/puppetlabs/bin/puppetserver ca list
```

Sign this request 

```
/opt/puppetlabs/bin/puppetserver ca sign --certname YOUR_SLAVE_NAME
```

* *Optional*

Run the below command to sign all servers 

```
/opt/puppetlabs/bin/puppetserver ca sign --all

```

Revoke by - 

```
/opt/puppetlabs/bin/puppetserver ca revoke --certname <AGENT_NAME>
```

List all certificates 

```
/opt/puppetlabs/bin/puppetserver ca list --all
```


* Test server - agent 

On the agent run the below - 

```
/opt/puppetlabs/bin/puppet agent --test
```

## Create your first manifest file 

On the master 

```
vi /etc/puppetlabs/code/environments/production/manifests/site.pp
```

Add the below lines to the file - 

```
node 'YOUR_SLAVE_NAME' { # Applies only to mentioned node. If nothing mentioned, applies to all.
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

## Install packages with puppet - 

Install mysql client 

```
vi /etc/puppetlabs/code/environments/production/manifests/site.pp
```

Add the below lines - 

```
 package { 'mysql-client' :
    ensure => present,
  }

```

On the agent server run - 

```
/opt/puppetlabs/bin/puppet agent --test
```

Install docker engine with puppet 

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


## Working with modules in puppet

Create a user with puppet

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

```
cd ../manifests
vi site.pp
```

Add the below lines - 

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

Now - add the include statement to site.pp at /etc/puppetlabs/code/environments/production/manifests

```
cd ../../../manifests/
vi site.pp
```

Add the below lines - 

```
include useraccount

```

On **Agent** machine run the below to avoid wait time for polling 

```
/opt/puppetlabs/bin/puppet agent -t

```























