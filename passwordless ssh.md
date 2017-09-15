# Build passwordless ssh between different nodes
# Eg: 节点rwsae01 与节点 rwsae03 手工SSH 配对

*在两台linux主机上由于环境的需要，经常要配置两台主机之间免密码登录，这就要用到key认证，也就是所谓的公私钥认证。便于理解，我这里指定两台主机为 A 和 B 。如果A主机想免密码登录到B主机上，则A主机上存放私钥，B 主机上存放公钥。通过ssh-keygen 命令生成的两个文件为：公钥文件 ~/.ssh/id_rsa.pub； 私钥文件 ~/.ssh/id_rsa 。而B主机上存放公钥时，需要将id_rsa.pub的内容存放到~/.ssh/authorized_keys 文件内，并且保证权限为600 。*

1. 查看节点rwsae01公钥
[rwsae01]:[00:19:23]:[~]:$ cat ~/.ssh/id_rsa.pub
```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC5C90E1Juyv0X+MKaWGMb5mot83yvZc4MpyQd5oiqfq2dr/gvqJE9uDnLCF1sFad0dRacjOnDEmK443C9blsakO4MjTBJtpgUeLBevaj+BW1/wDyyjqkDdw06HqrMoBwdczJN8/ye+jc53Xyw4cOfj4LWwihy3YtX660hF9BXJQ2oO/OwEXTKZpqW23v2D1JXPGN2cWabhE+I3T/2kIC0XUF9I9BGMZjRmTMSTkVs8eGe/DAvHyJirTodqQX4XM8ISd4TxXwjAbmiMA3PoHdykdAwMRR0m9wg1SL8zDR+0B0DRN9nGWmsH/AiFjn8xpHtMDfhCIFNXLWJjU5uCZ7GX racusr@rwsae01.us.oracle.com
```
2. 将节点rwsae01公钥复制到节点rwsae03
[Tue Aug 15 00:25:49 ][racusr@rwsae03.us.oracle.com:/scratch/oracle/OraHome]$vim ~/.ssh/authorized_keys 
```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEKnFeJw+UaEWLV5VUhBWQ4BfCjMqrGGfyKJZTGbnrEJCafXaQXEsCy67JwcAKgLWBuXg+yNTf/LrjhGAdO09RlSQSf699RaSY7umZ6uXZyqIonTk3H9qTxKFd4IwgJbDMvurZqBRuMRtC8EQCXmI1FVPDJm2KHHzPippvlW4w0Rl4cCwhM6VKzRnoKz/MxLB5oGiix4hZoEFdbVmwlBSQTNwLG4WJmd2l6NlJ/8MrYtGhMXYDGszl0mKcRSJzltL8APHYlf02p+6BtRwP/hxwig/D9WUcicZ+D/qcF+6OK7uV3tG57aaAeWTW+m6MjxGvoH8so7tY6cyqjeKuqOd5 racusr@rwsae04.us.oracle.com
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC5C90E1Juyv0X+MKaWGMb5mot83yvZc4MpyQd5oiqfq2dr/gvqJE9uDnLCF1sFad0dRacjOnDEmK443C9blsakO4MjTBJtpgUeLBevaj+BW1/wDyyjqkDdw06HqrMoBwdczJN8/ye+jc53Xyw4cOfj4LWwihy3YtX660hF9BXJQ2oO/OwEXTKZpqW23v2D1JXPGN2cWabhE+I3T/2kIC0XUF9I9BGMZjRmTMSTkVs8eGe/DAvHyJirTodqQX4XM8ISd4TxXwjAbmiMA3PoHdykdAwMRR0m9wg1SL8zDR+0B0DRN9nGWmsH/AiFjn8xpHtMDfhCIFNXLWJjU5uCZ7GX racusr@rwsae01.us.oracle.com                                              ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDECugqeQAzMdn3zlL9qJRB/EKWYl4ZgbWTZUv6F8eRnai5psDILPwGITN1s7ARr3we/jF9EcdGYSg6X5xpZXDMVQjKdYSYKc52ul+kAskqVrVtvWD6DiCvHwMed+y0GJw+WdHdPh7QhBhIohVsatNz+SIGiot6JMqcECCqIiJkUQrwlzhWw5v5iZ6gUiqdx5uEdhxhJ8/OWhOYq8MxxLdWY5/GoLV5yYlfkkGoVrutVKL3ZjPGmq2RhHjeblmJFGs7wYlBS7o/l42XIiJVMRUFu0HfQ7/bGzYEBYFgAGDAe3uTLgGqUjjaTReXj66FRf122o9fu8licwPky2wpRByj racusr@rwsae03.us.oracle.com
```
3. 重复上述步骤，将节点rwsae03公钥复制到节点rwsae01

[SSH Passwordless Login Using SSH Keygen in 5 Easy Steps](https://www.tecmint.com/ssh-passwordless-login-using-ssh-keygen-in-5-easy-steps/)
[linux配置ssh公钥认证](http://www.361way.com/ssh-public-key/3662.html)
[Online RSA Key Generator](http://travistidwell.com/jsencrypt/demo/)
