1.打开Git后先进行全局的用户名和邮箱的配置

```shell
git config --global user.name "Your Name"
git config --global user.email "email@example.com"
```

2.然后我们进行创建一个本地文件夹，以后把代码放里面

```shell
mkdir learngit
cd learngit
```

3.当创建好文件夹后我们需要把这个文件夹初始化为一个仓库

```shell
git init
```

4.添加文件，提交到本地仓库

```shell
 git add readme.txt
 git commit -m "wrote a readme file"
```

5.查看上述命令的结果,查看工作区的状态

```shell
git status
```

6.查看修改了什么代码

```shell
git diff readme.txt 
```

7.

















git密码输错的问题

首先，打开“控制面板”，把“查看方式”改为“大图标”，再打开“用户账户”

![123456](images/123456)