- 使用时,用chmod +x ca.sh,赋予文件可执行权限,
- 将ca.sh中的ip地址改为自己的ip地址
- ./ca.sh 运行
- 在弹出界面中输入4位以上密码.
- 然后一路回车,再根据提示输入上述密码.
- 最后,在当前文件路径下会生成server 和 client 两个文件夹,
server目录下存放的是:ca证书和服务器相关密钥证书.
client目录下存放的是:client相关密钥证书.

## 配合docker远程访问使用
- 将生成的server目录下的文件copy到/etc/docker 目录下。

- 修改文件 /lib/systemd/system/docker.service 

`vim /lib/systemd/system/docker.service`

- 将原来的： ExecStart=/usr/bin/dockerd -H fd://
改为：

`ExecStart=/usr/bin/dockerd --tlsverify --tlscacert=/etc/docker/ca.pem --tlscert=/etc/docker/server-cert.pem --tlskey=/etc/docker/server-key.pem -H tcp://0.0.0.0:2375 -H fd://`

- 重新启动 Docker

```
systemctl daemon-reload
systemctl restart docker.service
service docker restart
```

- 将client目录下的文件copy 到本地电脑上：如~/ca 目录下，
- 在idea 中使用时，安装好docker插件后，在配置项下：
tcp socket 中的Engin API URL:中输入： https://server's ip:2375

Certificates folder:中输入： ~/ca

- 正常的话，就会显示： Connection successful.
就可以远程对服务器进行安全的docker操作了。


- 参考 :

https://p0sec.net/index.php/archives/115/

https://blog.csdn.net/ChineseYoung/article/details/83107353


