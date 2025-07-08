# Ubuntu20.04 系统配置 PyTorch 环境（GPU 版）

> 原文地址：[https://blog.csdn.net/m0_55127902/article/details/135677560](https://blog.csdn.net/m0_55127902/article/details/135677560)

---

## 目录

- [一、关于 Ubuntu 和 NVIDIA 驱动](#一关于-ubuntu-和-nvidia-驱动)  
- [二、安装 CUDA](#二安装-cuda)  
  - [1. 下载 CUDA](#1-下载-cuda)  
  - [2. 安装 CUDA](#2-安装-cuda)  
  - [3. 添加环境变量](#3-添加环境变量)  
  - [4. 测试 CUDA 是否安装成功](#4-测试-cuda-是否安装成功)  
- [三、安装 cuDNN](#三安装-cudnn)  
  - [1. 下载 cuDNN](#1-下载-cudnn)  
  - [2. 安装 cuDNN](#2-安装-cudnn)  
  - [3. 测试 cuDNN 是否安装成功](#3-测试-cudnn-是否安装成功)  
- [四、安装 PyTorch](#四安装-pytorch)  
  - [1. 安装 Miniconda](#1-安装-miniconda)  
  - [2. 创建 PyTorch 虚拟环境](#2-创建-pytorch-虚拟环境)  
  - [3. 安装 PyTorch](#3-安装-pytorch)  
  - [4. 验证 PyTorch 安装成功](#4-验证-pytorch-安装成功)

---

## 一、关于 Ubuntu 和 NVIDIA 驱动

- 安装 Ubuntu（笔记本双系统）前，请查看与你品牌机型相匹配的教程。  
- 若安装后 Windows 引导项丢失，可在 Ubuntu 中修复或通过其他方式恢复。  
- 推荐从 NVIDIA 官网下载 `.run` 安装包手动安装驱动，不建议使用 `apt` 自动安装方式。  
- 如果黑屏，可以尝试切换独显直连或降级驱动。蓝牙、网络问题可更新驱动或 BIOS。  
- 不建议安装最新驱动，稳定性和兼容性更重要。  

安装 CUDA 前，先运行：

```bash
nvidia-smi
```

确认显卡及驱动状态正常，支持 CUDA 12.2。

---

## 二、安装 CUDA

### 1. 下载 CUDA

- 访问 [NVIDIA CUDA Toolkit Archive](https://developer.nvidia.com/cuda-toolkit-archive)，选择 CUDA 12.2.0。  
- 选择系统：Linux → x86_64 → Ubuntu → 20.04 → `runfile (local)`。

下载命令示例：

```bash
wget https://developer.download.nvidia.com/compute/cuda/12.2.0/local_installers/cuda_12.2.0_535.54.03_linux.run
```

### 2. 安装 CUDA

进入下载目录并运行：

```bash
sudo sh cuda_12.2.0_535.54.03_linux.run
```

安装过程中 **不要安装驱动组件**，否则会覆盖当前驱动。

### 3. 添加环境变量

```bash
sudo vim ~/.bashrc
```

添加以下内容：

```bash
export PATH=/usr/local/cuda-12.2/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-12.2/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

然后执行：

```bash
source ~/.bashrc
```

### 4. 测试 CUDA 是否安装成功

```bash
nvcc -V
```

输出 CUDA 版本即安装成功。

---

## 三、安装 cuDNN

### 1. 下载 cuDNN

- 访问 [NVIDIA cuDNN Archive](https://developer.nvidia.com/rdp/cudnn-archive)
- 登录后，选择对应版本（cuDNN 8.9.6 for CUDA 12.2）和 Ubuntu 20.04 `.deb` 文件。

### 2. 安装 cuDNN

```bash
sudo dpkg -i cudnn-local-repo-ubuntu2004-8.9.6.50_1.0-1_amd64.deb
sudo cp /var/cudnn-local-repo-ubuntu2004-8.9.6.50/cudnn-local-*-keyring.gpg /usr/share/keyrings
sudo apt update
sudo apt install libcudnn8 libcudnn8-dev libcudnn8-samples
```

### 3. 测试 cuDNN 是否安装成功

```bash
cd /usr/local/cuda-12.2/extras/demo_suite/
./bandwidthTest
./deviceQuery
```

若输出设备信息及成功信息，即说明安装成功。

---

## 四、安装 PyTorch

### 1. 安装 Miniconda

```bash
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm -rf ~/miniconda3/miniconda.sh
~/miniconda3/bin/conda init bash
~/miniconda3/bin/conda init zsh
```

然后重启终端或执行：

```bash
source ~/.bashrc
```

### 2. 创建 PyTorch 虚拟环境

```bash
conda create -n pytorch python=3.11
conda activate pytorch
```

### 3. 安装 PyTorch

```bash
conda install pytorch torchvision torchaudio pytorch-cuda=12.1 -c pytorch -c nvidia
```

> 注意：即使你安装的是 CUDA 12.2，PyTorch 官方仅提供 CUDA 12.1 的版本，实测兼容。

### 4. 验证 PyTorch 安装成功

```python
import torch
print(torch.__version__)
print(torch.cuda.is_available())
print(torch.backends.cudnn.version())
```

输出版本信息、`True` 和 cuDNN 版本号即表示安装成功。

---

## 参考与原文

- 原文链接：[https://blog.csdn.net/m0_55127902/article/details/135677560](https://blog.csdn.net/m0_55127902/article/details/135677560)
- CUDA 官网：[https://developer.nvidia.com/cuda-toolkit](https://developer.nvidia.com/cuda-toolkit)
- cuDNN 官网：[https://developer.nvidia.com/cudnn](https://developer.nvidia.com/cudnn)
- PyTorch 官网：[https://pytorch.org/get-started/locally/](https://pytorch.org/get-started/locally/)
