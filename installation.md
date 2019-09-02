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
echo "dns_alt_names = puppetserver" >>/etc/puppetlabs/puppet/puppet.conf

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
certname = YOUR_SLAVE_NAME
server = YOUR_MASTER_NAME
environment = production
runinterval = 15m
```

Start puppet agent 

```
/opt/puppetlabs/bin/puppet resource service puppet ensure=running enable=true
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

```
/opt/puppetlabs/bin/puppet agent --test
```

## Create your first manifest file 

```

```

























