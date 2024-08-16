---
title: Assimp 指南
date: 2022-09-08T14:17:00+08:00
categories: 
- C++
tags:
- Assimp
- C++
---

[learnopengl](https://learnopengl-cn.github.io/03%20Model%20Loading/01%20Assimp/) 介绍的 Assimp 已经很好了，Assimp 可以解析各种各样的三维模型文件，并转化为 Assimp 自己的数据结构。目前能解析的文件类型可以在[这里](https://assimp-docs.readthedocs.io/en/master/about/introduction.html)查看。补充：由于ply每个面相邻的顶点是共享的，可能会导致顶点纹理坐标等信息会遗失。

<!-- more -->

## 1 导入导出模型

```cpp
Assimp::Importer importer;
Assimp::Exporter exporter;
const aiScene *scene = importer.ReadFile("/OBJ/spider.obj", aiProcess_ValidateDataStructure);
exporter.Export(scene, "obj", "/OBJ/spider_out.obj");
```

Assimp 默认使用右手系（DirectX使用左手系），可以添加`aiProcess_MakeLeftHanded`到`ReadFile()`里面改为左手系。

Assimp 默认UV坐标系的原点在左下角，可以添加`aiProcess_FlipUVs`改为左上角。

全部的`aiProcess`标志我只在[这里](http://assimp.sourceforge.net/lib_html/postprocess_8h.html)找到了，后面用到什么标志再在上面更新一下。

解析失败后返回的scene为nullptr。

## 2 数据结构

最顶层是`aiScene`，所有的`Meshes`，`Materials`，`Textures`等都并列的存储在`aiScene`内。三维物体中的重复部分靠`aiScene`里面的`aiNode`树状结构体系索引来实现。`aiNode`引用`aiMesh`，`aiMesh`引用`aiMaterial`，并且`aiMesh`装载顶点、法向量、纹理坐标等信息。如下图所示。

![-](https://learnopengl-cn.github.io/img/03/01/assimp_structure.png)

以下是learnopengl中的介绍：

* 和材质和网格(Mesh)一样，所有的场景/模型数据都包含在Scene对象中。Scene对象也包含了场景根节点的引用。
* 场景的Root node（根节点）可能包含子节点（和其它的节点一样），它会有一系列指向场景对象中mMeshes数组中储存的网格数据的索引。Scene下的mMeshes数组储存了真正的Mesh对象，节点中的mMeshes数组保存的只是场景中网格数组的索引。
* 一个Mesh对象本身包含了渲染所需要的所有相关数据，像是顶点位置、法向量、纹理坐标、面(Face)和物体的材质。
* 一个网格包含了多个面。Face代表的是物体的渲染图元(Primitive)（三角形、方形、点）。一个面包含了组成图元的顶点的索引。由于顶点和索引是分开的，使用一个索引缓冲来渲染是非常简单的）。
* 最后，一个网格也包含了一个Material对象，它包含了一些函数能让我们获取物体的材质属性，比如说颜色和纹理贴图（比如漫反射和镜面光贴图）。

## 3 常用代码

Todo
