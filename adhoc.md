1. Get the uptime of remote hosts using ansible ad hoc command

```
ansible all -a "uptime"

OR 

ansible all -m "shell" -a "uptime"

OR 

ansible all -m command -a uptime
```

2. check the free memory

```
ansible all -m shell -a "free -m"
```

3. command to get physical memory 

```
ansible all -m shell -a "cat /proc/meminfo|head -2" 
```

4. Execute command as ROOT - become module

```
ansible all -m shell -a "cat /etc/passwd" -b 
```

5. Create a unix user group

```
ansible all -s -m group -a "name=weblogic state=present" 
```

6. Create a unix user 

```
ansible all -m user -a "name=weblogic group=weblogic createhome=yes" -b
```

7. Create a Directory with 755 permission

```
ansible all -m file -a "path=/tmp/demodir state=directory mode=0755" -b
```

8. Create a file with 755 permission

```
ansible all -m file -a "path=/tmp/demodir/testfile state=touch mode=0755"
```

9. Change ownership of a file

```
ansible all -m file -a "path=/tmp/demodir/testfile group=weblogic owner=weblogic" 
```

10. Install a package 

```
ansible all -m shell -a "apt-get update && apt-get install -y mysql-client" 

# To remove 

ansible all -m shell -a "apt-get update && apt-get remove  -y mysql-client"
```

11. Install a package using apt module 

```
ansible all -s -m apt -a "name=nginx state=installed"

# to remove 

ansible all -s -m apt -a "name=nginx state=absent"
```

12. Start and Stop service 

```
ansible all -m service -a "name=nginx state=started enabled=yes"

ansible all -m service -a "name=nginx state=stopped enabled=yes"
```

13. Fetch files from remote servers 

```
ansible all -m fetch -a "src=/etc/hosts dest=/home/weblogic/"
```

14. Copy files to remote servers 

```
ansible all -m copy -a "src=/etc/hosts dest=/tmp/"
```

15. Create symlinks 

```
ansible all -b -m file -a "src=/tmp/hosts dest=/home/weblogic/hosts owner=root group=root state=link force=yes"
```

16. Delete files/directories

```
ansible all -m file -a "dest=/tmp/test.txt state=absent"

```


