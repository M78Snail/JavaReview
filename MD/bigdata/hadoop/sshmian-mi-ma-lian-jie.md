保存密钥\(两边都做\)

```
ssh-keygen -t rsa
```

将生成的公钥文件拷贝过去（终端1）

```
cd /home/duxiaoming/.ssh
scp ./id_rsa.pub duxiaoming@192.168.25.136:/home/duxiaoming/.ssh/authorized_keys
```

修改权限

```
chmod 644 authorized_keys
```

```
-rw-r--r--. 1 root root  397 3月   9 17:40 authorized_keys
-rw-------. 1 root root 1675 3月   9 17:39 id_rsa
-rw-r--r--. 1 root root  397 3月   9 17:39 id_rsa.pub
