# Git LFS

Git LFS 是一种 Git 扩展，用于管理大型文件，通常用于版本控制中需要处理大文件的项目。
- [Git LFS](#git-lfs)
  - [前言:](#前言)
    - [读者疑惑:](#读者疑惑)
    - [笔者系统:](#笔者系统)
  - [Git LFS的安装:](#git-lfs的安装)
  - [使用示例--下载模型:](#使用示例--下载模型)
    - [1. 初始化 Git LFS:](#1-初始化-git-lfs)
    - [2. 克隆模型文件:](#2-克隆模型文件)
  - [使用示例--上传模型:](#使用示例--上传模型)

## 前言:

### 读者疑惑:

经常有读者疑惑自己已经安装Git了，为什么无法使用 `git clone` 拉取 Hugging Face 或 Model Scope 的模型文件。

这是因为 Git 默认只适合处理小文件，通常限制在 100 MB 左右。对于大文件，Git 可能会出现性能问题，需要安装 Git LFS 拓展来处理这些大文件。

### 笔者系统:

- Ubuntu 22.04


## Git LFS的安装:

终端输入下列指令:

```bash
sudo apt update
sudo apt install git-lfs
```

## 使用示例--下载模型:

### 1. 初始化 Git LFS:

初始化 Git LFS用于验证Git LFS已正常安装，让 Git 知道如何与 Git LFS 交互。指令如下:

```bash
git lfs install
```

### 2. 克隆模型文件:

以下载 Model Scope 的 [paraformer 模型](https://www.modelscope.cn/models/iic/speech_paraformer-large_asr_nat-zh-cn-16k-common-vocab8404-pytorch)为例:

```bash
git clone https://www.modelscope.cn/iic/speech_paraformer-large_asr_nat-zh-cn-16k-common-vocab8404-pytorch.git
```

终端输出:

```log
(base) root@ubuntu22:/data/paraformer# git clone https://www.modelscope.cn/iic/speech_paraformer-large_asr_nat-zh-cn-16k-common-vocab8404-pytorch.git
正克隆到 'speech_paraformer-large_asr_nat-zh-cn-16k-common-vocab8404-pytorch'...
remote: Enumerating objects: 466, done.
remote: Counting objects: 100% (71/71), done.
remote: Compressing objects: 100% (39/39), done.
remote: Total 466 (delta 40), reused 60 (delta 32), pack-reused 395
接收对象中: 100% (466/466), 1.12 GiB | 17.96 MiB/s, 完成.
处理 delta 中: 100% (268/268), 完成.
(base) root@ubuntu22:/data/paraformer# 
```


## 使用示例--上传模型:

你可以使用 Git LFS 来跟踪大型文件。例如，跟踪 `.bin` 文件类型：

```bash
git lfs track "*.bin"
```

然后添加 `.gitattributes` 文件并提交：

```bash
git add .gitattributes
git commit -m "Track large files with LFS"
```