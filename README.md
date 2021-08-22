##### Adhoc Commmands with Ansible

```
#to get the uptime

uptime

#ram usage

free -m

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
