---
title: "Github中的markdown渲染数学公式的注意事项"
date: 2025-04-02 16:35:24 +0800
tags: [GitHub, Workflow]
style: fill
color: primary
description: 
---

在上传至Github的`markdown`文件中，输入公式要特别注意，否则可能会导致在本地渲染成功，而github网页预览时渲染失败的问题。

例如

```markdown
first-order-accurate
$$(\frac{\partial u}{\partial x})_{i,j}=\frac{u_{i+1,j}-u_{i,j}}{\Delta x}+O(\Delta x)
$$
```

在本地vscode预览正常，应为

first-order-accurate

$$(\frac{\partial u}{\partial x}) _{i,j}=\frac{u _{i+1,j}-u _{i,j}}{\Delta x}+O(\Delta x)
$$

在Github网页预览时会渲染失败：

first-order-accurate
$$(\frac{\partial u}{\partial x})_{i,j}=\frac{u_{i+1,j}-u_{i,j}}{\Delta x}+O(\Delta x)
$$

此时需要将所有的下标（如`_{i,j}`）前添加空格，并在行间公式前后添加空行：

```markdown
first-order-accurate

$$(\frac{\partial u}{\partial x}) _{i,j}=\frac{u _{i+1,j}-u _{i,j}}{\Delta x}+O(\Delta x)
$$

```

即可恢复正常渲染。

first-order-accurate

$$(\frac{\partial u}{\partial x}) _{i,j}=\frac{u _{i+1,j}-u _{i,j}}{\Delta x}+O(\Delta x)
$$
