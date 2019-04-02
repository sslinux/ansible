# Templates

Jinja是基于Python的模板引擎。

template类是Jinja的另一个重要组件，可以看作一个编译过的模块文件，用来生产目标文本，传递Python的变量给模板去替换模板中的标记。


* 复制被管理端的配置文件到本地：

```bash
[root@blog playbooks]# scp root@192.168.1.44:/etc/httpd/conf/httpd.conf ./templates/
```

* 在管理端将配置文件要修改的地方定义成变量；

```bash
[root@blog playbooks]# sed "/^[ ]*#/d" templates/httpd.conf | sed "/^$/d"
ServerRoot "/etc/httpd"
Listen {{ http_port }}    # 定义的变量；
MaxClients {{ access_num }}  # 定义的变量；
KeepAlive On 
KeepAliveTimeout 60
MaxKeepAliveRequests 50
Include conf.modules.d/*.conf
User apache
Group apache
ServerAdmin root@localhost
ServerName {{ server_name }} # 定义的变量；
<Directory />
    AllowOverride none
    Require all denied
</Directory>
DocumentRoot "/var/www/html"
AddDefaultCharset UTF-8
<Directory "/var/www">
    options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>
<Directory "/var/cdrom">
   Options Indexes FollowSymLinks
   Require valid-user   
   Require all granted
   Order allow,deny
   Allow from all 
</Directory>
<Location /server-status>
    SetHandler server-status
    AuthType Basic
    AuthName "Server Status"
    AuthUserFile "/etc/httpd/conf/.htpasswd"
    Require valid-user
    Order deny,allow
    Allow from all
</Location>
<Directory "/var/www/html">
    Options Indexes FollowSymLinks
    AllowOverride None
    Order allow,deny
    Allow from 192.168.1.0/24
    Require all granted
</Directory>
<IfModule dir_module>
    DirectoryIndex index.html,index.php index.jsp
</IfModule>
<Files ".ht*">
    Require all denied
</Files>
ErrorLog "logs/error_log"
LogLevel warn
<IfModule log_config_module>
    LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" combined
    LogFormat "%h %l %u %t \"%r\" %>s %b" common
    <IfModule logio_module>
      LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\" %I %O" combinedio
    </IfModule>
    CustomLog "logs/access_log" combined
</IfModule>
<IfModule alias_module>
    ScriptAlias /cgi-bin/ "/var/www/cgi-bin/"
</IfModule>
<Directory "/var/www/cgi-bin">
    AllowOverride None
    Options None
    Require all granted
</Directory>
<IfModule mime_module>
    TypesConfig /etc/mime.types
    AddType application/x-compress .Z
    AddType application/x-gzip .gz .tgz
    AddType text/html .shtml
    AddOutputFilter INCLUDES .shtml
</IfModule>
AddDefaultCharset UTF-8
<IfModule mime_magic_module>
    MIMEMagicFile conf/magic
</IfModule>
EnableSendfile on
IncludeOptional conf.d/*.conf
```

* 在/etc/ansible/hosts中添加变量：

```bash
[root@blog playbooks]# vim /etc/ansible/hosts 
[root@blog playbooks]# head -2 /etc/ansible/hosts
[test]
192.168.1.44 http_port=192.168.1.44:80 access_num=100 server_name="blog.sslinux.com"
```

* 编写playbook：

此时复制配置文件时，使用的是template模块；

```yml
[root@blog playbooks]# cat template.yml
- hosts: test
  remote_user: root
  vars:
    - package: httpd
    - service: httpd
  tasks:
    - name: install httpd package
      yum: name={{ package }} state=latest
    - name: install configure file
      template: src=./templates/httpd.conf dest=/etc/httpd/conf/httpd.conf
      notify:
        - restart httpd
    - name: start httpd server
      service: name={{ service }} enabled=true state=started
  handlers:
    - name: restart httpd
      service: name={{ service }} state=restarted
```

```bash
[root@blog playbooks]# ansible-playbook template.yml

PLAY [test] ***************************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************************************************************************************************************************************
ok: [192.168.1.44]

TASK [install httpd package] **********************************************************************************************************************************************************************************************************************
ok: [192.168.1.44]

TASK [install configure file] *********************************************************************************************************************************************************************************************************************
ok: [192.168.1.44]

TASK [start httpd server] *************************************************************************************************************************************************************************************************************************
ok: [192.168.1.44]

PLAY RECAP ****************************************************************************************************************************************************************************************************************************************
192.168.1.44               : ok=4    changed=0    unreachable=0    failed=0   

# 验证一下文件的内容；

[root@blog playbooks]# ansible test -m shell -a 'cat /etc/httpd/conf/httpd.conf | grep "^Listen"'
192.168.1.44 | SUCCESS | rc=0 >>
Listen 192.168.1.44:80

[root@blog playbooks]# ansible test -m shell -a 'cat /etc/httpd/conf/httpd.conf | grep "^ServerName"'
192.168.1.44 | SUCCESS | rc=0 >>
ServerName blog.sslinux.com

[root@blog playbooks]# ansible test -m shell -a 'cat /etc/httpd/conf/httpd.conf | grep "^MaxClients"'
192.168.1.44 | SUCCESS | rc=0 >>
MaxClients 100

```