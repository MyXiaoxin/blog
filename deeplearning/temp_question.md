```
pic should be 2/3 dimensional. Got 4 dimensions
```

transform读取的是单个的照片，可以在  ____getitem____()方法中返回时添加transfrom

transform的维数应该是2维或者3维而不是进行批处理，把整个的批处理传进去是会报错的





