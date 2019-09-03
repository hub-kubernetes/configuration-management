# Install kubernetes with Ansible 

### Modify ansible inventory 

Add the below lines to `/etc/ansible/hosts`

```
[kuberneteshost]
YOUR_MASTER_HOST_NAME
YOUR_SLAVE_HOST_NAME
```

The output would be as below - 

```
[kuberneteshost]
master
slave
```



