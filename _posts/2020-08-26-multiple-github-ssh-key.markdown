---
layout: post
title:  "多个 GitHub SSH key 的配置"
date:   2020-08-26 20:30:30 +0800
categories: SSH GitHub
---
关于SSH key 的生成 GitHub上的指引已经很详细了 [Connecting to GitHub with SSH](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh), 下面主要记录在多 GitHub 帐号下的 SSH key 使用。
#### 在.ssh 目录下增加 config 文件， 写入以下配置, key1, key2 分别是生成 SSH key 时的私钥文件名


``` javascript 
#github
Host github.com-key1
HostName github.com
IdentityFile ~/.ssh/key1

#github2
Host github.com-key2
HostName github.com
IdentityFile ~/.ssh/key2
```

把key1, key2 对应的 key1.pub, key2.pub 公钥分别设置到对应的 GitHub 账号， 这样就可以正常用 Git 拉取不同账号下的仓库了。