---
title: "ssh更新"
created: '2022-01-15T17:14:59.097Z'
modified: '2022-01-15T18:39:15Z'
tags: [Notebooks/nb404, 新分区 1]
---

# ssh更新

1、首先将下载好的tar包上传到Linux上，我的tar包存放位置目录是：/opt/software

![None](@attachment/7ff1eb1c-9556-420f-908e-a6fc8b43aabb.png) 
4、安装openSSH之前，首先要安装 [OpenSSL](https://so.csdn.net/so/search?q=OpenSSL&spm=1001.2101.3001.7020) 。

 
 
1）解压上传好的tar包

[](@attachment/openssl-1.1.1l.tar.gz)
tar -zxvf ./openssl-1.1.1b.tar.gz -C ./

![None](@attachment/c750e7a6-94e5-42f8-9f4b-5bd9b92db501.png)（2）到解压目录下执行config

 
cdopenssl-1.1.1b

./config --prefix=/opt/modules/ssl/

![None](@attachment/6f9b75b3-bbce-46ac-89d7-934a168d60a6.png)（3）make

![None](@attachment/d54986f1-46dc-4029-a2e4-0b3ed9148c4d.png)（4）make install

![None](@attachment/045f0074-5e02-4c9d-8d63-d6c6b3455fd4.png)（5）执行以下命令查看安装后的版本

/opt/modules/ssl/bin/openssl version

发现此时报了如下的错误：

/opt/modules/ssl/bin/openssl:errorwhileloadingsharedlibraries: libssl.so.1.1: cannot opensharedobjectfile: No such fileordirectory

这是因为OpenSSL库的位置不正确导致的，可以使用如下命令创建软连接：

ln-s /opt/modules/ssl/lib/libssl.so.1.1 /usr/lib64/libssl.so.1.1

ln-s /opt/modules/ssl/lib/libcrypto.so.1.1 /usr/lib64/libcrypto.so.1.1

![None](@attachment/03b16781-c1b6-4572-98cf-c45938ddbd17.png)（6）将OpenSSL添加到环境变量

nano /etc/profile

在文件最后添加如下命令：

exportPATH="/opt/modules/ssl/bin:${PATH}"

![None](@attachment/783c723f-1d67-4e7f-ad4d-731c7e096127.png)保存并退出，source一下：

source~/.bashrc

查看环境变量是否添加成功：

whereis openssl

![None](@attachment/de659863-6a28-425f-b818-fb2d0fc5b326.png)查看OpenSSL的版本：

openssl version

![None](@attachment/173c11ab-0be0-4f51-9c14-be8cb64ee9a7.png)至此，OpenSSL升级成功。

 
5、接下来是OpenSSH的安装。

（1）解压OpenSSH的tar包并去到解压目录下

 
 
[](@attachment/openssh-7.9p1.tar.gz)
tar -zxvf openssh-7.9p1.tar.gz -C ./

cd openssh-7.9p1

（2）执行config

./configure --prefix=/opt/modules/ssh --sysconfdir=/etc/ssh --with-ssl-dir=/opt/modules/ssl

![None](@attachment/99ad1c5c-27fd-4590-aea3-ecac9a1d8e95.png)（3）make

![None](@attachment/a92a1afd-9c80-43dd-964d-3a36b2e53ca4.png)（4）make install

![None](@attachment/c44ae8a8-ac86-4753-aaac-ca8e2a6beeec.png)安装过程中会报一个权限的问题，此处可以先不用管，后面手动修改权限即可。

（5）验证版本

/opt/modules/ssh/bin/ssh -V

![None](@attachment/5d0fca2b-3f46-4318-8547-16ec5928505a.png)（6）设置sshd开机自启

cp /root/ssh升级/openssh-7.9p1/contrib/redhat/sshd.init /etc/init.d/sshd

chmod u+x /etc/init.d/sshd

cp /opt/modules/ssh/bin/* /usr/bin/

cp /opt/modules/ssh/sbin/sshd /usr/sbin/

chmod 600 /etc/ssh/*

nano /etc/ssh/sshd_config

PermitRootLogin yes

systemctl start sshd

systemctl statussshd

安装完成🤳

✔这是完成的可执行解压直接用：

[](@attachment/ssh升级.tar.gz)
 
