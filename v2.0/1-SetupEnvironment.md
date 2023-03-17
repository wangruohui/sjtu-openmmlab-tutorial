# 1. 使用预置环境

1. 登录 SJTU 集群
2. 激活预装环境
    ```bash
    module load anaconda3/2019.07
    source activate openmmlab2 # OpenMMLab v2.0 版本
    # source activate openmmlab # OpenMMLab v1.0 版本    
    ```

    注：方便起见可以在 `~/.bashrc` 中加载conda环境、配置快捷命令，例如在 `~/.bashrc` 中追加如下内容：
    ```
    module load anaconda3/2019.07
 
    alias sa='source activate'
    alias sda='source deactivate'
    ```
    保存退出，**重新登陆**后默认加载 Anaconda ，且可以使用 `sa openmmlab2` 激活对应环境
3. 可以使用 `mim list` 查看所有预装的 openmmlab v2.0 工具包

虽然环境里面预装了一系列工具包，但我们也可以使用github上最新的代码。例如可以通过以下方式“覆盖”预装的代码：

```bash
git clone https://github.com/open-mmlab/mmclassification.git
cd mmclassification
# 在此目录下 import mmcls 就会使用该文件夹下的版本
python -c "import mmcls; print(mmcls.__file__)"
# Output: '/cluster/home/it_stu80/mmclassification/mmcls/__init__.py'
```

如果有多个源码包，也可以配置 PYTHONPATH

```
cd ~
export PYTHONPATH=~/mmclassification;~/mmdetection
python -c "import mmcls; print(mmcls.__file__)"
```

# 2. 自己配置环境

## 2.1. 准备工作

### 2.1.1. 加载 conda 环境

命令：`module load anaconda3/2019.07`

可以配置在 `~/.bashrc` 里面

<!-- 可能需要执行一次 conda init 在 bashrc 里面配置 conda -->

### 2.1.2. （可选）更换国内镜像

默认源在国外，速度比较慢

1. 更换 conda 源

    创建或编辑 `~/.config/pip/pip.conf` 文件，加入或修改 `index-url` 相关段落为：
    ```
    [global]
    index-url = https://mirror.sjtu.edu.cn/pypi/web/simple
    format = columns
    ```
    参考：https://sjtug.org/post/mirror-help/pypi/

2. 更换 pip 源

    创建或编辑 `~/.condarc` ，添加：
    ```
    default_channels:
    - https://mirror.sjtu.edu.cn/anaconda/pkgs/r
    - https://mirror.sjtu.edu.cn/anaconda/pkgs/main
    custom_channels:
    conda-forge: https://mirror.sjtu.edu.cn/anaconda/cloud/
    pytorch: https://mirror.sjtu.edu.cn/anaconda/cloud/
    channels:
    - defaults
    ```
    参考：https://mirror.sjtu.edu.cn/docs/anaconda

## 2.2. 创建 Python 虚拟环境

**命令**：`conda create -n mm2 python=3.9`

- 这里 `mm2` 为自定义的环境名称，可以更改为别的
- Python 版本也可以换别的，但建议装一个目前主流版本

创建成功后可 `source activate mm2` 激活虚拟环境

激活成功后可以验证一下 `which pip`，`which python` 等路径在新创建的环境之下，之前好像是有过 `PATH` 没有激活的情况，示例：

```
(mm2) [it_stu80@head2 mmclassification]$ which python
~/.conda/envs/mm2/bin/python
(mm2) [it_stu80@head2 mmclassification]$ which pip
~/.conda/envs/mm2/bin/pip
```

## 2.3. 安装 PyTorch

- MMDeploy 要求 1.8 到 1.12
- 驱动版本限制 CUDA 不超过 10.2 

所以，需要需要装个旧版的 PyTorch ，可参考 https://pytorch.org/get-started/previous-versions/，`torchaudio` 不用装

示例命令：
```
source activate mm2 # 注意激活环境
conda install pytorch==1.12.0 torchvision==0.13.0 cudatoolkit=10.2 -c pytorch`
```

验证（注意激活mm2环境）：
```
srun -p gpu -N 1 --gres gpu:1 python -c 'import torch; print(torch.cuda.is_available())'
```

安装正常应输出 `True`

也可以写个程序跑一下，例如：

创建 `test_torch.py`，内容：

```py
import torch
a = torch.ones(4)
print(a)
print(a.cuda())
print(torch.cuda.is_available())
```

保存后，运行命令 `srun -p gpu -N 1 --gres gpu:1 python test_torch.py`

## 2.4. 安装 OpenMMLab 2.0

### 2.4.1. 安装 MMCV

MMCV 是 OpenMMLab 的基础库，正常可以通过 `mim` 工具安装，但 `mim` 在管理节点上识别不到 GPU，所以需要手动安装对应版本的 MMCV>=2.0，可参考 https://github.com/open-mmlab/mmcv#installation

示例命令：`pip install https://download.openmmlab.com/mmcv/dist/cu102/torch1.12.0/mmcv-2.0.0rc4-cp39-cp39-manylinux1_x86_64.whl`，注意这里面 CUDA 版本和 Python 版本应与上述步骤中的 PyTorch 保持一致。

验证：`srun -p gpu -N 1 --gres gpu:1 python -c "import mmcv.ops"`，安装正常情况下无输出，安装不成功会报错。

### 2.4.2. 安装算法工具包

1. 可以直接用 pip 安装，例如：

`pip install "mmdet>=3.0.0rc0" "mmcls>=1.0.0rc0"`

注意双引号

检查：`pip list | grep mm`

正常输出：
```
mmcls               1.0.0rc5
mmcv                2.0.0rc4
mmdet               3.0.0rc6
mmengine            0.6.0
```

2. 也可以从源码安装，例如：

```
git clone https://github.com/open-mmlab/mmclassification.git
cd mmclassification
pip3 install -e . # 注意
# 若环境中存在其他版本的 mmclassificaition，
# 这一步也可以不执行，但需要在 mmclassification 运行代码，
# 以保证 import 的是 clone 下来的版本
```

### 2.4.3. 配置 MMDeploy (on going)

1. 加载CUDA环境，可写在bashrc中

    ```
    module load anaconda3/2019.07
    module load cuda/10.2
    module load cudnn/8.2.0
    module load gcc/7.3.0
    ```

2. mmdeploy 1.x


    ```
    git clone https://gitee.com/open-mmlab/mmdeploy.git -b 1.x
    cd mmdeploy/third_party/
    git clone https://gitee.com/NVIDIA_Developer_Community/cub.git
    git clone https://gitee.com/lkhlll/pybind11.git
    git clone https://gitee.com/rocket-booster/spdlog.git
    ```
    
    ```
    # onnxruntime
    wget https://gh.flyinbug.top/gh/https://github.com/open-mmlab/mmdeploy/releases/download/v1.0.0rc2/mmdeploy-1.0.0rc2-linux-x86_64-onnxruntime1.8.1.tar.gz
    cd mmdeploy-1.0.0rc2-linux-x86_64-onnxruntime1.8.1
    pip install dist/mmdeploy-1.0.0rc2-py3-none-linux_x86_64.whl
    pip install sdk/python/mmdeploy_python-1.0.0rc2-cp39-none-linux_x86_64.whl
    cd ..

    pip install onnxruntime==1.8.1
    wget https://gh.flyinbug.top/gh/https://github.com/microsoft/onnxruntime/releases/download/v1.8.1/onnxruntime-linux-x64-1.8.1.tgz
    tar -zxvf onnxruntime-linux-x64-1.8.1.tgz
    export ONNXRUNTIME_DIR=$(pwd)/onnxruntime-linux-x64-1.8.1
    export LD_LIBRARY_PATH=$ONNXRUNTIME_DIR/lib:$LD_LIBRARY_PATH
    ```

    ```
    # tensorrt
    wget https://gh.flyinbug.top/gh/https://github.com/open-mmlab/mmdeploy/releases/download/v1.0.0rc2/mmdeploy-1.0.0rc2-linux-x86_64-cuda10.2-tensorrt8.2.3.0.tar.gz
    tar -zxvf mmdeploy-1.0.0rc2-linux-x86_64-cuda10.2-tensorrt8.2.3.0.tar.gz
    cd mmdeploy-1.0.0rc2-linux-x86_64-cuda10.2-tensorrt8.2.3.0
    pip install dist/mmdeploy-1.0.0rc2-py3-none-linux_x86_64.whl
    pip install sdk/python/mmdeploy_python-1.0.0rc2-cp39-none-linux_x86_64.whl
    cd ..

    # nvidia
    tar -zxvf TensorRT-8.2.3.0.Linux.x86_64-gnu.cuda-10.2.cudnn8.2.tar.gz
    pip install TensorRT-8.2.3.0/python/tensorrt-8.2.3.0-cp39-none-linux_x86_64.whl
    export TENSORRT_DIR=$(pwd)/TensorRT-8.2.3.0
    export LD_LIBRARY_PATH=${TENSORRT_DIR}/lib:$LD_LIBRARY_PATH
    pip install pycuda

    export CUDNN_DIR=/cluster/apps/cudnn/8.2.0
    export LD_LIBRARY_PATH=$CUDNN_DIR/lib64:$LD_LIBRARY_PATH

    # custom operators
    cd ${MMDEPLOY_DIR}
    mkdir -p build && cd build
    cmake -DCMAKE_CXX_COMPILER=g++ -DMMDEPLOY_TARGET_BACKENDS=ort -DONNXRUNTIME_DIR=${ONNXRUNTIME_DIR} ..
    make -j$(nproc) && make install

    cd ${MMDEPLOY_DIR}
    mkdir -p build && cd build
    cmake -DCMAKE_CXX_COMPILER=g++ -DMMDEPLOY_TARGET_BACKENDS=trt -DTENSORRT_DIR=${TENSORRT_DIR} -DCUDNN_DIR=${CUDNN_DIR} ..
    make -j$(nproc) && make install

    cd ${MMDEPLOY_DIR}
    pip install -e .
    ```