# 1 .transform

torchvision.transforms是pytorch中的图像预处理包有很多图像预处理方法, 今天从图像维度出发, 讲一些我们经常会用到的方法，比如说数据中心化（仅减均值），数据标准化（减均值，除以标准差），随机裁剪，旋转，镜像等一系列操作。一般会用到如下顺序的操作：

>1.随机裁剪
>
>2.ToTensor
>
>3.数据标准化（减均值，除以标准差）

-  **Resize**, 把给定的图片resize到target size
-  **Normalize**, Normalized an tensor image with mean and standard deviation
-  **ToTensor**, convert a PIL image to tensor (H, W, C) in range [0,255] to a torch.Tensor(C, H, W) in the range [0.0, 1.0]
-  **ToPILImage**, convert a tensor to PIL image
-  **CenterCrop**, 在图片的中间区域进行crop
-  **RandomCrop**, 在一个随机位置进行crop
-  **FiveCrop**, 把图像裁剪为四个角和一个中心
-  **TenCrop**, five crop+flip 1->10

### 1.1 ToTensor & ToPILImage

  从Numpy到Tensor的转换有两种方法, 可以用torch.from_numpy(ndarray), 也可以用ToTensor()(ndarray), 这两种有什么区别呢?

```python
np_data = np.random.random((260, 194, 3))
# np_data size (height, width, channel)
torch_data = torch.from_numpy(np_data)  # numpy -> tensor 1
# torch_data size [260, 194, 3]
torch2array = torch_data.numpy()
# torch2array size[260, 194, 3]
tensor_data = ToTensor()(np_data)    # numpy -> tensor 2
#tensor_data size[3, 260, 194]
tensor2array = tensor_data.numpy()
#tensor2array size[3, 260, 194]
```

  可以看出, 如果用torch.from_numpy()的方法, 转换的tensor和ndarray是相同的shape, `(height, width, channel)`的shape进, 仍然变成`(height, width, channel)`的shape
   但是, 如果是用ToTensor()的方式, 那就是 `(height, width, channel)`的shape进,  `(channel, height, width)`的shape出
   不同于numpy->tensor有两种做法, 从tensor->numpy却只有一种, 就是tensor.numpy(), 而这种做法的shape前后也是不会变化的, 所以我们看到如果按照ToTensor() -> tensor.numpy()的方式将np转成tensor再转为np, 那shape和一开始的np是不同的, 就是`(height, width, channel)`会变成`(channel, height, width)`

  下面我们举一个实际的读图转换的例子

```
# PIL -> np -> tensor -> PIL
PIL_img = Image.open('test_pic.jpeg')
#PIL_img size [194, 260, 3], range [0,255]
np_img = np.asarray(PIL_img)
# np_img size [260, 194, 3], range [0,255]
tensor_from_np = ToTensor()(np_img)
# tensor_img size [3, 260, 194], range [0,1]
tensor_from_PIL = ToTensor()(PIL_img)
# tensor_img size [3, 260, 194], range [0,1]
PIL_from_tensor = ToPILImage()(tensor_from_np)
# PIL_from_tensor size [194, 260, 3], range[0,255]
PIL_from_tensor.save('img_from_tensor.bmp')

# tensor -> numpy -> PIL
np_from_tensor = tensor_from_np.numpy()
# np_from_tensor size [3, 260, 194], range [0, 1]
np_from_tensor *= 255
# np_from_tensor size [3, 260, 194], range [0, 255]
np_from_tensor = np.transpose(np_from_tensor, (1, 2, 0))
# np_from_tensor size [260, 194, 3], range [0, 255]
PIL_from_np = Image.fromarray(np.uint8(np_from_tensor))
# PIL_from_np size [194, 260, 3], range [0, 255]
PIL_from_np.save('img_from_np.bmp')
```

  为了说明PIL/np/tensor相互转换之间产生的shape和range的变化, 我们这个例子稍微复杂了一点, 关键就是三点

- 用ToTensor()转换, 不管input的是PIL还是np, 只要是uint8格式的, 都会直接转成[0,1]的float32格式, 所以我们看到的tensor_from_np和tensor_from_PIL是一样的, 而ToPILImage是将tensor转为PIL Image, 自动又会把float32的[0,1]转回uint8
- 如果是先把tensor转为np, 再转为PIL Image, 那就有上面我们提到的np_img和tensor_from_np.numpy()的shape和range都是不同的, 因为.numpy()的过程就是把tensor格式的数据复制到np格式的数据, 重要的是, **np_from_tensor和tensor_from_np共享了同一段内存, 所以我们可以看到在更改np_from_tensor的值的时候, tensor_from_np也在变**, 所以要进行[0,1]->[0,255] + transpose更改shape + float32->uint8的过程改为和np_img的shape和range都一样, 这个时候再用Image.fromarray()才能还原成和PIL_img相同的图像
- ToPILImage除了可以做tensor->PIL, 还可以做np->PIL, 就是将shape为(C,H,W)的Tensor或shape为(H,W,C)的numpy.ndarray转换成PIL.Image, 但注意, 输入是np的话它不会把[0,1]float32自动转成uint8, 所以用法就变成和Image.fromarray()一样了

```
#before np_from_tensor
tensor_from_np2 = tensor_from_np.clone()
...
...
...
np_from_tensor2 = tensor_from_np2.numpy()
np_from_tensor2 *= 255
np_from_tensor2 = np.transpose(np_from_tensor2, (1, 2, 0))
PIL_from_np2 = ToPILImage()(np.uint8(np_from_tensor2))
PIL_from_np2.save('img_from_np2.bmp')
```

**Note:**
   这个np.transpose()函数很有用, opencv的图像shape和np也不一样, 也需要用这个函数来转换维度
   上面提到.numpy()的做法是让tensor和np.ndarray()共享内存, 所以如果我们要用tensor原来的数据, 那就要在对np_from_tensor处理之前, 先用.clone()的方式复制出一个副本来

### 1.2 Resize

torchvision.transforms.Resize(size, interpolation=2)
   这个resize的函数很简单, 用的就是PIL图像处理的内核, 要注意的是, 这个size的格式是`(height, width)`, 也就是说加入要用这个函数2倍downsample一个输入PIL Image图像, 应该是这样

```
input_image = Image.open('image path')
Resize((input_image.size[1], input_image.size[0]))(input_image)
#or
Resize((input_image.height, input_image.width))(input_image)
```

  推荐用第二种形式, 比较清楚地体现PIL Image和tensor的维度差别

### 1.3 Crop

  torchvision.transforms里有很多crop的方法, 选几个用的多的说一下

-  **CenterCrop**, 在图片的中间区域进行crop
-  **RandomCrop**, 在一个随机位置进行crop
-  **FiveCrop**, 把图像裁剪为四个角和一个中心
-  **TenCrop**, 在FiveCrop的基础上，再将输入图像进行水平或竖直翻转，然后再进行FiveCrop操作，这样一张输入图像就能得到10张crop结果

| Crop                                   | Description                                                  |
| -------------------------------------- | ------------------------------------------------------------ |
| CenterCrop(size)                       | 将给定的`PIL.Image`进行中心切割，得到给定的`size`，`size`可以是`tuple`，`(target_height, target_width)`。`size`也可以是一个`Integer`，在这种情况下，切出来的图片的形状是正方形。 |
| RandomCrop(szie, padding=0)            | 切割中心点的位置随机选取。`size`可以是`tuple`也可以是`Integer` |
| FiveCrop(size)                         | 把图像裁剪为四个角和一个中心, 1张图生成5张图                 |
| TenCrop(size, vertical_flip=False)     | 在`FiveCrop`的基础上，再将输入图像进行水平或竖直翻转，然后再进行`FiveCrop`操作，这样一张输入图像就能得到10张crop结果 |
| RandomSizedCrop(size, interpolation=2) | 先将给定的`PIL.Image`随机切，然后再`resize`成给定的`size`大小 |

### 1.4 Normalize

在跑一些模型的时候，发现有如下的代码：

```python
transform_train = transforms.Compose([
    transforms.RandomCrop(32, padding=4),
    transforms.RandomHorizontalFlip(),
    transforms.ToTensor(),
    transforms.Normalize((0.4914, 0.4822, 0.4465), (0.2023, 0.1994, 0.2010)),
])
```

最后一行代码知道是对数据进行标准化，可能不知道这些数据是怎么来的，后来才知道这些数据是要自己进行计算的，计算法方法如链接[所示](code/normalize.py)。

### 1.5 Compose

  transforms.Compose()就是把以上提到的多个步骤整合到一起, 按顺序执行

```python
transforms.Compose([
    transforms.CenterCrop(10),
    transforms.ToTensor(),
    transforms.Normaliza()
])
```