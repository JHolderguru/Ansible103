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

ansible servers -m fetch -a "src=/home/ansadmin/db.conf dest=./demo/{{inventroy_hostname}}_db.conf flat=yes"

```


#### 7. Create/Delete Dir
#### Syntax
```
#touch
#file
ansible databases -m file -a "path=/home/ansadmin/demo.txt state=touch"


ansible databases -m file -a "path=/tmp/hello.txt state=touch"

#Create another file with different permissions
#mode

ansible databases -m file -a "path=/tmp/hello.txt state=touch 'mode=0777'"

#To remove file
#absent
ansible databases -m file -a "path=/tmp/hello.txt state=absent"

#Create a DIR
#directory
#ansible databases -m file -a "path=/tmp/hello state=directory"
```

#### 8. Sudo priviledges on root
```
#Providing the acc has been placed on sudoers
#visudo
#-b
ansible databses -m file -a "path=/etc/demo.txt state touch" (will throw an error)

ansible databses -m file -a "path=/etc/demo.txt state touch" -b
```
#### 9. Installing a package
```
#Systems: yum
#apt
#other states :absent, present, removed, latest

ansible databases:servers -m yum -a "name=git state=present" -b
```
#### 10. Gather Facts (sys info)

```
ansible databases -m setup
#filter by arg_value

ansible databases -m setup -a "filter=ansible_architecture"

#check versions on remote node
ansible servers -m shell -a "git --version"
ansible servers -m shell -a "https --version"


#To create customs fact of the 2 above

#1. Create Dir /etc/ansible/facts.d
mkdir facts.d

vi git_httpv.fact
#2. Inside of facts.d place one or more customs facts files with extensions

#!/bin/bash
git_ver=$(git --version | awk '{print $3}')
httpd_ver=$(http -version | awk 'NR==1 {print $3}')

cat << EOF
{"Git_version": "$git_ver",
 "httpd_version": $httpd_ver"

}
EOF



#3. Should be json.
#4 the fact file should have execution.
chmod 755 git_httpv.fact

#Creating this file on remote notes
ansible all -m file -a "path=/etc/ansible/facts.d state=directory" -b

#Copy script to remote Nodes
ansible all -m copy -a "src=/etc/ansible/facts.d/git_httpv.fact dest=/etc/ansible/facts.d/git_httpv mode='0755'" -b

#Collecting the Custom fact
```
