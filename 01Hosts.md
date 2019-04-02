# Hosts

在playbook中的每一个play都可以选择在哪些服务器和以什么用户完成，hosts一行可以是一个主机组、主机、多个主机，中间以冒号分隔，可使用通配模式。

其中remote_user表示执行的用户账号。

```yaml
[root@blog playbooks]# cat test.yml 
- hosts: test
  remote_user: sslinux
  become: yes
  become_user: root
  become_method: su
  tasks:
    - name: list files
      command: ls /var/log/
```

remote_user 指定远程主机执行的用户名；

user相关的信息要结合/etc/ansible/hosts文件或自定义的文件相结合；



检查语法：

```bash
[root@blog playbooks]# ansible-playbook --syntax-check test.yml 

playbook: test.yml
```

执行playbook：

```bash
[root@blog playbooks]# ansible-playbook test.yml 

PLAY [test] ***************************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************************************************************************************************************************************
ok: [192.168.1.44]

TASK [list files] *********************************************************************************************************************************************************************************************************************************
changed: [192.168.1.44]

PLAY RECAP ****************************************************************************************************************************************************************************************************************************************
192.168.1.44               : ok=2    changed=1    unreachable=0    failed=0   


```
