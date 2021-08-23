##### Adhoc Commmands with Ansible

```
#to get the uptime

uptime

#ram usage

free -m

syntax:
ansible [-i dev_inv] servers:databases -m module [-a arg_value]
```
#### 1. Shell script - you can combined the above Commands in a shell script file.

cat multi_task.sh
```
uptime
free -m

```

#### 2.uptime and memory playbook.yml

```yml
---
 - name: Finding uptime and free ram
   hosts: all
   tasks:
 - name: Finding uptime
   shell: uptime
   register: up_time
 - debug:
     var:up_time.stdout_lines
 - name: Finding Ram info
   shell: free -m
   register: ram_info
 - debug:
     var:up_time.stdout_lines

```

#### 3. To see other modules apart from ' shell module'

```
ansibledoc -l
```


#### 4. keeping the module file in the /temp nodes.
#### every adhoc commad is kept temporarily in the .ansible/temp folder
```
ANSIBLE_KEEP_REMOTE_FILES=1 ansible all -m shell -a "uptime"

```

#### 5.Copying Files from Engine to Nodes.
##### -using copy module
```
ansible databases -m copy -a "src=/source/file/path dest=/dest/location"
ansible databases -m copy -a "src=./hosts dest=dest/temp/"

#creating content on remote Nodes
ansible databases -m copy -a "content='hello this is test content' dest=dest/temp/hello.txt"

#Updating the file overwrites it but to keep the previous file as a back up
#creating content on remote Nodes
ansible databases -m copy -a "content='hello this is test content' dest=dest/temp/hello.txt backup=yes"

#Copy into new DIR
ansible databases -m copy -a "content='This is a file in the home dir' dest=/home/ansadmin/db.conf"

#verify ssh ipadress
#navigate to DIR
```
#### 6.Copying Files from our Nodes to Engine.
#### -using the Fetch module

```
ansible [inventory_file] <databases:servers> <module> [-a arguments]

ansible databases -m fetch -a "src=/source/file/path dest=/dest/location"

#Copying the same from different servers/databases

ansible servers -m fetch -a "src=/home/ansadmin/db.conf dest=./demo/{{inventroy_hostname}}_db.conf"

```
