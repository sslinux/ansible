# ansible


* 安装ansible：

```bash
yum install -y ansible
```

* 生成秘钥：
```bash
[root@blog ~]# ssh-keygen -t rsa -b 4096
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
Created directory '/root/.ssh'.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:kZ1Pjw011sv9NbrxJ7kGCxjICvBS3xM0WUyNbaDtAmY root@blog.sslinux.com
The key's randomart image is:
+---[RSA 4096]----+
|     .o=+=    +. |
|. .   o+oo+. o ..|
| + .Eo.o+.o o . o|
|. oo..=... o = +o|
| . . ...So  o + +|
|    .  .. . .o  .|
|           . o+. |
|            ..+..|
|             ..o.|
+----[SHA256]-----+

```

* 添加主机：

```bash
[root@blog ~]# vim /etc/ansible/hosts 
[root@blog ~]# cat /etc/ansible/hosts
[test]
192.168.1.44
```

* 通过秘钥认证：

```bash
# 该操作只做一次：
[root@blog ~]# ssh-copy-id root@192.168.1.44
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/root/.ssh/id_rsa.pub"
The authenticity of host '192.168.1.44 (192.168.1.44)' can't be established.
ECDSA key fingerprint is SHA256:ZKtnI/gOmqd/yik0QlkzbIJzIUt4qbXMD6F5p2opGJs.
ECDSA key fingerprint is MD5:cf:70:20:b0:af:82:43:fc:44:72:bc:20:a2:50:e7:68.
Are you sure you want to continue connecting (yes/no)? yes   # 接收秘钥
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
root@192.168.1.44's password:     # 需要输入密码；

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'root@192.168.1.44'"
and check to make sure that only the key(s) you wanted were added.
```

