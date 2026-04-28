# 快捷用法
## 新建仓库

```
echo "# start" >> README.md
git init

git config user.name userxxx
git config user.email abc@example.com

git config user.signingkey <GPG KEY ID>
git config commit.gpgsign true

git add README.md
git commit -m "first commit"
git branch -M main

git remote add origin git@github.com:userxxx/git-usage.git
git remote set-url --add --push origin git@github.com:userxxx/git-usage.git
git remote set-url --add --push origin ssh://git@codeberg.org/userxxx/git-usage.git

git push -u origin main
```

## 克隆仓库

## 后续操作

# git用法

列出所有全局配置

```
git config --global --list
```

全局与本地（当前文件夹）的区别在于有没有 --global ，本地配置会覆盖全局配置。

配置用户名和邮箱
```
git config user.name userxxx
git config user.email abc@example.com
```

此处是git提交的用户名和邮箱，涉及个人隐私，个人小项目可以自己编一个，但是如果用GPG密钥则需要真实邮箱。

添加GPG密钥，在每次提交时签名。
```
git config user.signingkey <GPG KEY ID>
git config commit.gpgsign true
```
windows踩坑经验，需要配置GPG路径
```
git config
```

克隆仓库
```
git clone git@github.com:userxxx/git-usage.git
```

查看远程仓库信息
```
git remote -v
```

添加多个远程仓库
```
git remote add origin git@github.com:userxxx/git-usage.git
git remote set-url --add --push origin git@github.com:userxxx/git-usage.git
git remote set-url --add --push origin ssh://git@codeberg.org/userxxx/git-usage.git
```

拉取（pull，远程->本地）和推送（push，本地->远程）
```
git pull origin main
git push origin main
```

git删除远程仓库
```
git remote rm <仓库别名>
```

git切换分支
```
git switch main
```

git基本工作流
```
# 查看文件状态（红色=未跟踪/修改，绿色=已暂存）
git status

# 将文件添加到暂存区
git add <文件名>      # 单个文件
git add .            # 所有变更（新文件+修改+删除）

# 提交到本地仓库
git commit -m "描述本次修改的内容"

# 查看提交历史
git log --oneline    # 简洁版
```

远程仓库操作
```
# 添加远程仓库
git remote add origin <URL>

# 推送到远程（首次需要 -u 建立关联）
git push -u origin main

# 之后直接推送
git push

# 拉取远程更新
git pull              # 拉取并合并
git fetch             # 仅下载，不合并
```


# create SSH Key

```
ssh-keygen -t ed25519 -a 100
```

一般情况下直接enter即可，但若有默认的ssh公私钥，则需要更改路径。如果/home/user/.ssh/id_ed25519存在，被问及是否overwrite the existing file，键盘输入n选择不重写。重新执行ssh-keygen -t ed25519 -a 100，并输入新路径，例如/home/user/.ssh/id_ed25519_xxx 。

将$HOME/.ssh/id_ed25519_xxx.pub上传到github或codeberg上，可以cat或记事本查看文件内容并复制。不要上传私钥（没有.pub，文件名类似id_ed25519_xxx）。

为确保使用正确的公钥登录远程git仓库，需编辑 ~/.ssh/config文件，没有此文件则需要创建。
```
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_xxx

Host codeberg.org
    HostName codeberg.org
    User git
    IdentityFile ~/.ssh/id_ed25519_xxx
```
保证Host、HostName和IdentityFile（私钥路径）对应。

# GPG密钥
安装GnuPG

查看有没有GPG密钥对
```
gpg --list-secret-keys --keyid-format LONG
```

## 创建GPG密钥

```
gpg --full-generate-key
```
键盘输入1和Enter选择RSA and RSA.

选择密钥大小，推荐4096 bits，按下Enter以确认。

选择有效期，推荐1-2年，0表示无限期，按下Enter以确认。

检查选择是否正确，输入y，按下Enter以确认。

输入你的信息，**注意邮箱和github/codeberg账号一致**，用户名可以不一致。

输入一个密码；请务必将它记在安全的地方。之后，你需要用它来把密钥添加到 Git，或者在密钥泄露时用来撤销它。

## 添加GPG密钥

在终端中输入 `gpg --list-secret-keys --keyid-format LONG`。

选择你想要使用的密钥（很可能是你刚刚生成的那一个）。在这个例子中，GPG 密钥 ID 是 `3AA5C34371567BD2`：

```
$ gpg --list-secret-keys --keyid-format LONG
/home/knut/.gnupg/pubring.kbx
--------------------------
sec   rsa4096/3AA5C34371567BD2 2021-06-06 [SC] [expires: 2022-06-06]
      6CD8F2B4F3E2E8F08274B563480F8962730149C7
uid                 [ultimate] knut <knut@codeberg.org>
ssb   rsa4096/42B317FD4BA89E7A 2021-06-06 [E] [expires: 2022-06-06]
```

在终端中输入 `gpg --armor --export <GPG 密钥 ID>`。这将输出你的公共密钥。

复制输出的内容，它以 `-----BEGIN PGP PUBLIC KEY BLOCK-----` 开头，以 `-----END PGP PUBLIC KEY BLOCK-----` 结尾。

在Github/Codeberg中添加GPG密钥。

Codeberg可能需要验证SSH和GPG密钥，Github目前不需要。

## 导入和导出GPG密钥

SSH密钥直接复制即可，GPG密钥比较麻烦。导出私钥：
```
gpg --armor --export-secret-key <GPG 密钥 ID> > my_private_key.asc
```

导入私钥
```
gpg --import my_private_key.asc
```
导入后，GPG 只认识这个密钥，但信任级别是未知的。可以手动将它的信任设为 ultimate（最终信任，即信任自己是所有者）。但实测不影响git提交。


# codeberg

SSH  ssh://git@codeberg.org/userxxx/git-usage.git

HTTPS  https://codeberg.org/userxxx/git-usage.git

## 从命令行创建一个新的仓库

```
touch README.md
git init
git switch -c main
git add README.md
git commit -m "first commit"
git remote add origin ssh://git@codeberg.org/userxxx/git-usage.git
git push -u origin main
```

## 从命令行推送已经创建的仓库

```
git remote add origin ssh://git@codeberg.org/userxxx/git-usage.git
git push -u origin main
```

# github

SSH  git@github.com:userxxx/git-usage.git

HTTPS  https://github.com/userxxx/git-usage.git

## create a new repository on the command line

```
echo "# git-usage" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:userxxx/git-usage.git
git push -u origin main
```

## push an existing repository from the command line

```
git remote add origin git@github.com:userxxx/git-usage.git
git branch -M main
git push -u origin main
```

