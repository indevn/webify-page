---
title: Chinagraph2020笔记：真实感渲染科研入门
author: Lecture by Lingqi Yan
date: '2022-07-07'
categories:
  - Rendering
tags:
  - blogdown
  - Markdown
  - MathJax
  - Pandoc
  - RStudio
  - toc
---



## Introduction

渲染的过程：计算光线传播的过程

渲染的科研，主要集中于真实感Realism和速度Speed。

渲染在科研上尚未解决的问题：

- Tradeoff is costly
- Simplification is far from easy（可参考卡通渲染等）

## Prepared Knowledge

1. Real-time/Offline graphics pipeline

2. Texture Mapping 定义和记录逐点属性

3. Bidirectional Reflectance Distribution Function, BRDF

4. 采样理论（PDF采样）与蒙特卡洛积分

   实际基于大数定律，实验结果很多的时候将会接近期望值。可以使用置信区间进行评估，也可以使用期望验证无偏性（期望值与实际相符合）。

   1. 采样过程的优化：多重重要性采样

   2. 如何进行取样？引入**Discrepancy**: 

      how uniformly distributed a sequence of point is $D_n^*=D_n^*(x_1,...,x_n)$

      用于描述实际和理论的差异，则引出Low Discrepancy Sequences 低差异化序列。

      - Pros：Infinite, …
      - Cons: Patterns, degeneration in high dimentions

5. 直接光照和全局光照

   全局光照的获得：路径追踪 Path Tracing

   - Matter: 对于定义在半球的积分进行采样
   - 一般的求解思路是递归的求解，如今则更多使用更高维的概念来描述Path（光线每弹射一次则增高两个维度，所有Path的弹射区间被称为“Path Space”）

6. 材质 Materials

   - Microfacet Material
     - Normal Distribution Function, NDF 描述物体表面的复杂特征
   - Rendering Materials
     1. Evaluation 估计，BRDF的值是多少
     2. Sampling 采样 如何对其做重要性采样
     3. PDF of Samping 假设知道入射光，需要用其他方法进行采样，我们需要首先知道其PDF

   - Appearance Modeling and Synthesis

## State of the art rendering research

Basic Ideas: 

- Appearence Modeling
- Light Transport
- Representation(e.g. <u>describe</u> ambient light better)
- Approximation



**Offline Rendering**

- Light transport

  *Light Transport Simulation with Vertex Connection and Merging*

  *Path Guiding in Production*

- Material modeling

  e.g. Glossary可以由无数微小表面的反射进行呈现，我们可以做到很细致的效果

  *A Efficient and Practical Near and Far Field Fur Reflection Model*

- Sampling

- Denoising

  KPCN: Kernel-Predicting Convolutional Networks for Denoising Monte Carlo Renderings

**Real-time Rendering**

- PRT: 预计算

- RTX：实时光线追踪

  - Light Transport

    *Spatiotemporal reservoir resampling for real-time ray tracing with dynamic direct lighting*

    https://www.cnblogs.com/Liuwq/p/15974079.html

  - Denoising

    代表性的两种方法：传统方法和深度学习方法

    （传统方法，更快）*SVGF: Spatiotemporal Variance-Guided Filtering: Real-Time Reconstruction for Path-Traced Global Illumination*

    （深度学习方法，需要跑一遍神经网络）*Interactive Reconstruction of Monte Carlo Image Sequences using a Recurrent Denoising Autoencoder*

**Interdisciplinary**

- Differentiable Rendering 可微渲染
- Measurement&Construction
- Neural Rendering (actually not rendering)

## Tools chain

**Renderer**:

An integration of Light transport algorithms, Materials, Scene handler

Mitsuba 0.5.0/0.6.0 is in general a good choice; PBRT is also good.



**Ray Tracer:** 

(CPU, Intel’s) Embree，尽量在CPU中完成

(GPU) OptiX *(cross platform)*, DXR *(DirectX Raytracing, Windows Only)* 

*Falcor is a good framework*



对于光栅化的实时渲染研究，可以自己写一套（使用OpenGL/DirectX/Vulkan），其中如果使用OpenGL，可以尝试imGUI或NanoGUI，提供了基本的GUI。



**Other tools**

- To view HDR文件(.exr) ： Tev
- To do fastest GPU inference：NVIDIA’s TensorRT



## Resources

**Scenes**:

同时可以尽量学习使用游戏引擎并使用其shaders。为了使用场景，尽量学习3D软件如Blender，Maya等，并且购买模型并将其导成自己渲染器的格式。同时我们也能使用一些免费的渲染库：

- The Stanford 3D Scanning Repository
- Benedikt Bitterli’s rendering resources
- NVIDIA’s ORCA



经典算法实现：

工业界不允许共享代码，渲染科研人员往往不倾向于共享代码。非常经典的算法包含在不同的渲染器当中。



## Main Rendering Venus

常见会议：Siggraph, Siggraph Asia, EG, EGSR, PG,HPG,I3D

期刊：ToG, CGF, TVCG(only in China)



## Training

代码训练方案：

- Offline

  - Write a path tracer from scratch using Intel’s Embree

  - 引入多重重要性采样

  - 支持微表面材质

  - 实现正确的BDPT(Bidirectional Path Tracing)

- Real-Time

  - Write C++ OpenGL wrapper
  - 支持arcball support，对其添加软阴影VSSM（Shader）
  - 在OpenGL程序中支持Optix
  - 写一个实时光线追踪，用OpenGL做Primary，后续用Optix
  - 添加降噪（按SVGF）



## Future Thoughts

**Topic**:

- “Frankenstein” type 缝合怪

  e.g. Vertex Connection and Merging (A+B)

- “Nature porter” type 跨领域的方法迁移

- “Dig the grave” type 解决过去方法/条件不能解决的理论

- “Deep replacement” type

  e.g. Neural Importance Sampling

- “Add prefix” type

  e.g. Anisotropic Spherical Gaussains

[HARD] Never the best, but the first



**Future**:

Geometry&Appearance

Procedural Rendering 实时生成&渲染细节

VR/AR需要差分渲染Differentiable Rendering
