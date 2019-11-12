conda删除所有源：
conda config --remove-key channels
conda添加清华源：
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --set show_channel_urls yes
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/
conda安装pytorch：
cPU版本：conda install pytorch torchvision cpuonly -c pytorch
gPU版本：conda install pytorch torchvision cudatoolkit=10.1 -c pytorch
conda安装TensorFlow(安装最新版本的)
conda install tensorflow
conda install tensorflow-gpu
如果指定版本，需要在后面加上TensorFlow=1.14.0即可
