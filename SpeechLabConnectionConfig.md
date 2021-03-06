Procedure of first connect speechlab cluster 20220421

# Get the account, Brief info
user_id: yjl00
user_ip: `ssh -p 5566 -Y id@202.120.38.69`
init_pswd: / a mask for speechlab / 
user_pswd: 5+6

Manual of ssh explanation of argu `-Y`
```bash
-Y      Enables trusted X11 forwarding.  Trusted X11 forwardings are not subjected to the X11 SECURITY extension controls.`

             (Debian-specific: This option does nothing in the default configuration: it is equivalent to “ForwardX11Trusted yes”,
             which is the default as described above.  Set the ForwardX11Trusted option to “no” to restore the upstream behaviour.
             This may change in future depending on client-side improvements.)
```

wiki_ip: `http://202.120.38.10:10780`
wiki_pswd: same to cluster

拿到账号后先在终端测试一下能否正常连接，然后用`passwd id`修改了密码，正常

# First Link with VSCode

With extension _remote SSH_:
侧边栏-remote explorer-齿轮-C:\Users\Excespo\.ssh\config-添加如下配置：
```
Host SpeechLab
  HostName 202.120.38.69
  Port 5566
  User yjl00
```

但是这样没有处理推荐链接ssh指令中的`-Y`指令 #TODO

# SSH-key

这样的登录方式需要选择系统类型，验证密码，并有一段等待时间，所以我们需要配置SSH密钥

在yjl00的home目录中创建.ssh文件夹（注意yjl00的绝对路径还是有一点复杂的 `/mnt/xlancefs/home/yjl00`）

创建SSH公钥文件。在本机使用指令`ssh-keygen`后，公钥文件存放在`C:\Users\Excespo`下（由于之前已经创建过SSH，创建方式没有详细记载）

正确创建后，文件夹中有id_rsa.pub，将这个文件cp到`/home/.ssh/`下（结尾是excespo@qq.com的是yoga机生成的key）

此处简介`scp`的用法 `scp -v -P port_id source target` 例如在`yijie@202.120.55.134:/home/speechLab` 下有test_trans.txt，用scp传到`yjl00@202.120.38.69:/mnt/xlancefs/home/yjl00`下，对应指令为
`scp -v -P 5566 test_trans.txt yjl00@202.120.38.69:/mnt/xlancefs/home/yjl00`,需要输入密码

生成authorized_keys，指令`cat id_rsa.pub >> ~/.ssh/authorized_keys`，修改权限`chmod 600 authorized_keys`,`chmod 700 -R .ssh`,用`ls -l`检查权限是否设置成功

检查SSH-key是否设置成功,成功！

#TODO 如何管理多个SSH-key?
（over write - `cat <your_key >~/.ssh/authorized_keys`, append - `cat <your_key >>~/.ssh/authorized_keys`)

# From Jump Machine to Target

一样的，这次使用内网ip
```
 Host xlance-Date
    HostName 192.168.1.6
    User yjl00
    ProxyCommand ssh -W %h:%p xlance-Gauss
```
即可

# Inner web ssh login

从跳板机ssh免密登录！步骤同上，略过。很方便的是甚至不需要传id_rsa.pub因为两边Home是共享的（应该是这个道理）

