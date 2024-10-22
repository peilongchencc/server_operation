# 连接AWS远程服务器

以Mac为例，当你获得`.pem`(私钥时)指令如下：

```bash
mv aistar-aws-server.pem ~/.ssh/
chmod 400 ~/.ssh/aistar-aws-server.pem
ssh -i ~/.ssh/aistar-aws-server.pem ubuntu@ec2-18-217-75-46.us-east-2.compute.amazonaws.com
```

如果上述指令提示:

```log
sign_and_send_pubkey: no mutual signature supported
ubuntu@ec2-18-217-75-46.us-east-2.compute.amazopnaws.com: Permission denied (publickey)
```

该问题是由于 `ssh` 客户端和 AWS EC2 实例的默认配置之间的兼容性问题。具体来说，较新的 OpenSSH 版本(例如，VScode的 Remote-SSH 插件)默认禁用了某些老旧的算法和密钥类型，比如 `ssh-rsa`。而 AWS 上的某些 EC2 实例，特别是较老版本的实例或配置，仍然依赖 `ssh-rsa` 算法进行身份验证和密钥交换。因此，你需要通过配置 `PubkeyAcceptedKeyTypes` 和 `HostKeyAlgorithms` 来明确允许 `ssh-rsa` 算法。

解决办法：

在 `~/.ssh/config` 文件中添加以下配置：

```conf
Host *
  PubkeyAcceptedKeyTypes=+ssh-rsa
  HostKeyAlgorithms=+ssh-rsa
```

这两行配置分别允许客户端接受 `ssh-rsa` 公钥和使用 `ssh-rsa` 算法进行主机密钥验证。这是必要的，因为默认情况下，OpenSSH 的新版本禁止这些算法。
