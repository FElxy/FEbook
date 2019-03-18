# 在github中使用多个ssh key管理不同repo

github限制了不同账号不能使用相同的ssh key，否则会报Key already in use的错误

```shell
cd ~/.ssh
ssh-keygen -t rsa -C "user@example.com"
```

添加到 SSH Agent 代理

```shell
ssh-add ~/.ssh/id_rsa_v
```

创建或者修改config文件

```shell
touch ~/.ssh/config
```

```shell
# 默认的 github 用户
Host github.com
 HostName github.com
 User git
 IdentityFile ~/.ssh/id_rsa
 
# 使用 id_rsa_v 验证的 github 别名
Host my.github.com
 HostName github.com
 User git
 IdentityFile ~/.ssh/id_rsa_v

```

使用的时候替换服务器的别名

```
git@github.com => git@my.github.com
```

验证

```shell
ssh -T git@my.github.com
// You've successfully authenticated, but GitHub does not provide shell access.
```

