---
layout: post
title: '玩一玩Neural Style'
tags: [CV, tensroflow, machine learning, promgramming]
comments: true
---

尝试了一下机器学习的乐趣

### 一、TensorFlow Windows 下安装

- 我选择的是 Anaconda 的安装方法，他会自动安装 Python 环境，我用的是 64 位的 Anaconda3 4.1.0，Python3.5.1
- 之后安装 TensorFlow
- > Pip install tensorflow
- or
- > Pip install tensorflow-gpu
- pip 可能需要更新
- 下的可能比较慢，也可以去 github 上 clone 一个。
- 因为我有 N 卡，所以 CPU 版本和 GPU 版本都试了一下。GPU 版本需要安装 CUDA8 和 cuDnn 库，库文件放在 CUDA8 的安装目录下，而且需要继续添加环境变量。

### 二、neural-style

- neural-style 是一个风格学习项目，好像是后来搬到 TensorFlow 上的。
- [项目链接](https://github.com/anishathalye/neural-style)
- neural-style 依赖 VGG19 的深度学习库（Pre-trained VGG network 在项目主页有提供下载）[VGG19 主页](http://www.vlfeat.org/matconvnet/pretrained/#downloading-the-pre-trained-models)
- 之前 GPU 版本的环境变量一直配置不好，所以最后用了 CPU 版本。

- > python neural_style.py --content /content file/ --styles /style file/ --output /output file/

- 在项目下填入训练风格输出文件等就 OK 了

![image](https://raw.githubusercontent.com/Kivior/kivior.github.io/master/images/TF-ns.jpg)

- 我用蒙娜丽莎做风格样本学习，在对梦露做风格处理，看上去效果一般，也许调教一下小参数会更好，但是笔记本的低压 U 在 iterations 设为 1000 时跑了 6 个小时 emmm...
