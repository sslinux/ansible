# Tasks

* 1.Play的主体部分是task列表，task列表中的各任务按次序逐个在hosts中指定的主机上执行，即在所有主机上完成第一个任务后再开始第二个任务。

    在运行playbook时（从上到下执行），如果一个host执行task失败，整个tasks都会回滚，请修正playbook 中的错误，然后重新执行即可。

    Task的目的是使用指定的参数执行模块，而在模块参数中可以使用变量，模块执行时幂等的，这意味着多次执行是安全的，因为其结果一致。

* 2.每一个task必须有一个名称name，这样在运行playbook时，从其输出的任务执行信息中可以很好的辨别出是属于哪一个task的。如果没有定义name，‘action’的值将会用作输出信息中标记特定的task。

* 3.定义一个task，常见的格式:”module: options” 例如：yum: name=httpd

* 4.ansible的自带模块中，command模块和shell模块无需使用key=value格式


## 常用命令：

选项： 

* -k(--ask-pass) 用来浇花输入ssh密码；
* -K(-ask-become-pass)用来交互输入sudo密码；
* -u 指定用户

* ansible-playbook a.yml --syntax-check   检查yaml文件的语法是否正确；
* ansible-playbook a.yml --list-task      检查tasks任务；
* ansible-playbook a.yml --list-hosts     检查生效的主机；
* ansible-playbook a.yml --start-at-task='Copy nginx.conf' 指定从某个task开始运行；


###  egnore_errors

play中只要执行命令的返回值不为0，就会报错，tasks停止，可以添加下面

ignore_errors: True  # 忽略错误，强制返回成功


```yaml
[root@blog playbooks]# cat test.yml 
- hosts: 192.168.1.44
  remote_user: sslinux
  become: yes
  become_user: root
  become_method: su
  tasks:
    - name: list files
      command: ls /var/logs/
      ignore_errors: True
    - name: echo test
      command: echo "hello ansible"
```

