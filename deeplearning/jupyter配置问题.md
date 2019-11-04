本文是在anaconda的环境下配置的，装好anaconda后，jupyter-notebook就自带装上了。我们在使用python的过程中往往需要使用虚拟环境，在终端下激活虚拟环境很方便，但是在jupyter-notebook中需要进行一些配置方可使用。

在进行环境配置的时候经常用到一些命令，每次都需要查找，并且都会遇到一些问题，这次记录下来，以后如果用到相应的命令的时候可以直接使用，同时把一些坑记录下来。

1.安装好anaconda之后创建python环境，这个环境中可以添加自己要安装的软件。

```python
conda create -n noname python=3.7
```

2.添加ipykernel，它可以生成kernel并把它写道jupyter中

```python
pip install ipykernel
```

3.安装jupyter环境，在jupyter中使用

```python
ipykernel install --name pytorch
```

本文的配置环境有些特殊。我使用的是一个服务器（Ubuntu16.04）上的普通账户，anaconda由管理员安装在了根目录下。普通账户在使用anaconda新建虚拟环境时，产生的相关文件都在本账户的家目录下，即/home/username。但是在生成ipykernel的配置文件时，上述命令生成配置文件的路径为/usr/local/share/jupyter，显然普通账户没有权限写入，因而报错

[Errno 13] Permission denied: '/usr/local/share/jupyter'

为此，需要添加--user选项，将配置文件生成在本账户的家目录下。

python -m ipykernel install --user --name py27-caffe-notebook

至此，添加kernel完成。

查看已有的kernel

jupyter kernelspec list

删除已有的kernel

jupyter kernelspec remove kernelname

以上的命令删除仅仅是配置文件，并没有卸载相应虚拟环境的ipykernel，因此若要再次安装相应python虚拟环境的kernel，只需激活虚拟环境，然后

python -m ipykernel install --name kernelname





清华镜像的使用方法

```
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple some-package
```





