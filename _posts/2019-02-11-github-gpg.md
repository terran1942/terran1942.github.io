---
layout: post
title:  "使用GPG对GitHub commit进行签名"
date:   2019-02-11 14:57:46 +0800
categories: GitHub
---

![gpg](/assets/2019-02-11/gpg.png)

# GPG历史
GPG的历史可以追溯到**RSA加密演算法**。最早提供RSA加密的数字签名软件有两个，除了RSA公司外，另一个软件就是**PGP**（Pretty Good Privacy）。这个PGP是由程序员**菲尔·齐默尔曼**编写并免费使用。但是，齐默尔曼做的这个PGP软件，既没有RSA的专利权，也没被RSA公司授权过。也就是说，这是一个侵权事件。所以，自由软件基金会决定，开发一个PGP的替代品，取名为GnuPG。这就是GPG的由来。

# GitHub与GPG
在GitHub上新建repository、合并pull request时，提交会有“Verified”标记，这里就是GitHub使用GPG进行过签名的commit。但是用户提交默认是不进行签名的。下文中会对签名提交进行说明。
![GitHub signed commit](/assets/2019-02-11/snapshot-0.png)

# 使用GPG对commit进行签名
#### 安装GPG
macOS系统上使用Homebrew安装GPG

``` shell
brew install gpg
```

#### 生成密钥

``` shell
gpg --full-generate-key
```
``` shell
 gpg (GnuPG) 2.2.12; Copyright (C) 2018 Free Software Foundation, Inc.  
 This is free software: you are free to change and redistribute it.  
 There is NO WARRANTY, to the extent permitted by law.  
 请选择您要使用的密钥类型：  
   (1) RSA 和 RSA （默认）  
   (2) DSA 和 Elgamal  
   (3) DSA（仅用于签名）  
   (4) RSA（仅用于签名）   
 您的选择是？  
```
默认选择第一个选项，表示加密和签名都使用RSA算法。

然后，设置密钥的长度。

``` shell
RSA 密钥的长度应在 1024 位与 4096 位之间。  
您想要使用的密钥长度？(2048)   
```

密钥越长越安全，默认是2048位。这里按照GitHub help中的要求选择**4096位**。

设定密钥的有效期。
``` shell
请设定这个密钥的有效期限。  
         0 = 密钥永不过期  
      \<n>  = 密钥在 n 天后过期  
      \<n>w = 密钥在 n 周后过期  
      \<n>m = 密钥在 n 月后过期  
      \<n>y = 密钥在 n 年后过期  
密钥的有效期限是？(0)   
```

这里选择0即可。  
之后确认信息，按照提示输入“真是姓名”、“电子邮箱地址”、“注释”，这里需要注意**电子邮箱地址**要填写GitHub账号所用的邮箱。  
之后输入密钥密码，按照提示操作制造随机数，等待密钥生成结束。

#### 列出密钥
--list-keys参数列出系统中已有的密钥．

``` shell
gpg --list-keys
```

显示结果

``` shell
/Users/yongliang/.gnupg/pubring.kbx
-----------------------------------
pub   rsa4096 2019-02-11 [SC]
      64B2AFFC86E139D677DEC8D7C6F5CE74976A40B8
uid           [ 绝对 ] yongliang li <terran1942@gmail.com>
sub   rsa4096 2019-02-11 [E]
```

#### 导出密钥
公钥文件（.gnupg/pubring.kbx）以二进制形式储存，--armor参数可以将其转换为ASCII码显示。

``` shell
gpg --armor --output public-key.txt --export [uid]
```

"用户ID"指定哪个用户的公钥，output参数指定输出文件名（public-key.txt）。  
类似地，export-secret-keys参数可以转换私钥。

``` shell
gpg --armor --output private-key.txt --export-secret-keys [uid]
```

#### 导入密钥
需要导入密钥，使用--import参数
``` shell
gpg --import [密钥文件]
```

#### 在GitHub导入公钥
在 GitHub - Settings - SSH and GPG keys 中添加GPG公钥。
![new gpg key](/assets/2019-02-11/snapshot-2.png)

#### 设置Git
``` shell
git config user.name yongliang li
git config user.email terran1942@gmail.com
git config commit.gpgsign true
git config user.signingkey 64B2AFFC86E139D677DEC8D7C6F5CE74976A40B8
```

#### 提交更改
设置Git之后，提交更改。使用-S参数使用签名，之后按提示输入生成密钥时的密码。
``` shell
git -S -m "add github pgp"
```

提交后在GitHub上查看提交记录，显示该提交已使用GPG签名。
![commit history](/assets/2019-02-11/snapshot-3.png)

# 常见问题
macOS操作系统上，如果出现签名失败错误，需要在~/.bash_profile中加入：
``` shell
export GPG_TTY=$(tty)
```

# Github其他设置
在保护分支规则中可以找到“require signed commit”选项。要求commit必须经过签名，防止发生篡改。
![GitHub signed commit](/assets/2019-02-11/snapshot-1.png)

# 参考链接
+ [RSA加密演算法](https://zh.wikipedia.org/wiki/RSA%E5%8A%A0%E5%AF%86%E6%BC%94%E7%AE%97%E6%B3%95)
+ [菲尔·齐默尔曼](https://zh.wikipedia.org/wiki/%E8%8F%B2%E7%88%BE%C2%B7%E9%BD%8A%E9%BB%98%E7%88%BE%E6%9B%BC)
+ [GPG入门教程](http://www.ruanyifeng.com/blog/2013/07/gpg.html)
+ [GitHub Help](https://help.github.com/articles/managing-commit-signature-verification/)