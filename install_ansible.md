# Ansible Installation and Configuration 

## Set up SSH between master and slave machine 

For this demo we will use root account to setup ssh - 

* We will first start with **master** server - 

```
# Log in as root 

sudo -i

# Generate SSH keys 

ssh-keygen 

# Press enter to put default values while creating ssh 

# Create authorized keys 

cd .ssh 

cat id_rsa.pub >> authorized_keys

chmod 600 * 

# Copy the value for id_rsa.pub
# Make sure that it is one single line and not multiple lines. 

cat id_rsa.pub 
```

* Now log in to **slave** server - 

```
# Log in as root 

sudo -i 

cd .ssh

# Copy the content of id_rsa.pub from master to authorized_keys file 

echo "CONTENT_OF_MASTER_id_rsa.pub" >> authorized_keys
```

Your output should look like below - 

```
echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCwyy1bNWjMCzyjEnr/v6Khcc5UPXV3FgyMBgjQsxrv3zVn6XyPeDJw557vn5dXMsHjUa2ZZafGlM36lw5MAa4SNyDEMzS/8tXKfmQ7W+8Rs7Uto2ON5X2Z7YNxqqdzVL7DNc61i08dPoaaq6OXqljhV4x72DIWIidDFEFaX2qRAwlDRzD1ayqpxzP+yRlF3yUpdMqj2sMFpQBiD6cCsOtNOhAfFl2snAC8WaxKjXj1pOFrEoXe11F8irIla4OTs6sRO+77jY8zq8I8/j3lkyeggjoFaSkypux+NTxgWbKKKCySff1YmtzUbsSnISIUGmHN7q5B5sF916tPhraptUxD root@master" >> authorized_keys
```

Verify successful copy of keys by running 

```
cat -E authorized_keys
```

The output you will get is - 

```
cat -E authorized_keys 

ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCwyy1bNWjMCzyjEnr/v6Khcc5UPXV3FgyMBgjQsxrv3zVn6XyPeDJw557vn5dXMsHjUa2ZZafGlM36lw5MAa4SNyDEMzS/8tXKfmQ7W+8Rs7Uto2ON5X2Z7YN
xqqdzVL7DNc61i08dPoaaq6OXqljhV4x72DIWIidDFEFaX2qRAwlDRzD1ayqpxzP+yRlF3yUpdMqj2sMFpQBiD6cCsOtNOhAfFl2snAC8WaxKjXj1pOFrEoXe11F8irIla4OTs6sRO+77jY8zq8I8/j3lkyeggj
oFaSkypux+NTxgWbKKKCySff1YmtzUbsSnISIUGmHN7q5B5sF916tPhraptUxD root@master$

# Note the $ sign at the end. 
# if you dont see the $ sign this means that the copy wasnt successful
# Please ensure that you have one single line 
```

Change the permission of the file - 

```
chmod 600 authorized_keys
```

* Verify connectivity from **master** to **slave** 

Log in to master

```
# Log in as root 

sudo -i 

# Perform ssh to slave 

ssh root@YOUR_SLAVE_MACHINE_NAME hostname 

# Enter yes - when prompted for - Permanently added 'slave,10.142.0.23' (ECDSA) to the list of known hosts.
```

Output should be as below if all above steps are performed successfully 

```
ssh root@slave hostname
slave
```

* Verify connectivity from **master** to **master**

```
# Log in as root 

sudo -i 

# Perform ssh to master 

ssh root@YOUR_MASTER_MACHINE_NAME hostname 

# Enter yes - when prompted for - Permanently added 'master,10.142.0.23' (ECDSA) to the list of known hosts.

```



This configures ssh between master and slave for root account - 


## Install ansible 

> Since Ansible uses ssh connectivity to interact with slave nodes - ansible installation will be done only on the master

```
sudo apt update

sudo apt install -y software-properties-common

sudo apt-add-repository ppa:ansible/ansible

# Press Enter key when prompted

sudo apt update

sudo apt-get install -y ansible

```

## Set up Ansible inventory - 

> Ansible inventory is the file where you can specify which hosts to connect to. Ansible inventory is stored at the location. Default location of the inventory file is - `/etc/ansible/hosts` , however you can create this file anywhere and use the `-i ` flag with ansible command to specify the location of your custom inventory file. 

> Ansible inventory allows you to specify individual hosts as well as group of hosts. Referencing to a group allows you to run the commands on a group of hosts. 

Set up the inventory - 

```
vi /etc/ansible/hosts
```

Delete all lines ( ``dd`` command on vi editor ) 

Add the below lines 

```
YOUR_MASTER_MACHINE_NAME 

YOUR_SLAVE_MACHINE_NAME 

[webserver]
YOUR_MASTER_MACHINE_NAME

[database]
YOUR_SLAVE_MACHINE_NAME

[dockerhost]
YOUR_MASTER_MACHINE_NAME
YOUR_SLAVE_MACHINE_NAME

```

Your output should look like below - 

```
master

slave

[webserver]
master

[database]
slave

[dockerhost]
master
slave

```

## Execute basic ansible commands - 

```

# Ping master and slave hosts - 

ansible -m ping master 

ansible -m ping slave 

# Ping all the groups 

ansible -m ping webserver 

ansible -m ping database

ansible -m ping dockerhost

```


























