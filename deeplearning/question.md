可以自定义transformer使用compose把他们包起来

```
pic should be 2/3 dimensional. Got 4 dimensions
```

transform读取的是单个的照片，可以在  ____getitem____()方法中返回时添加transfrom，在transformer中进行处理的时候

```
can't handle the data type
```

transform的维数应该是2维或者3维而不是进行批处理，把整个的批处理传进去是会报错的

Windows10中num_workers的数量如果大于0可能会报错，不知道为什么

数据集的处理可以使用继承Dataset的方法来进行处理

net.train()和net.eval()是干什么的

```
RuntimeError: Given groups=1, weight of size 6 3 5 5, expected input[128, 1, 32, 32] to have 3 channels, but got 1 channels instead
```

The error is basically what the message says.
Your input has 4 channels while the first conv layer expects its input to have 1 channel.
Change `self.cnn1` to `nn.Conv2d(in_channels=4, ...)` and try it again.





