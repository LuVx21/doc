---
title: module
tags:
- Python
---

# 模块发布

1. module目录结构

    ```
    ├── setup.py
    ├── dir1
    │   ├── aa.py
    │   └── __init__.py
    └── dir2
        ├── bb.py
        └── __init__.py
    ```

2. setup.py

    ```python
    from distutils.core import setup

    setup(name="LuVx", version="1.0", description="this is description", author="F.LuVx", py_modules=['dir1.aa', 'dir2.bb'])
    ```

3. 构建模块

    ```
    python setup.py build
    ```

4. 打包压缩

    ```
    python setup.py sdist
    ```

最终生成文件`LuVx-1.0.tar.gz`

# 模块安装

解压,进入
```
# 可以指定安装目录
python setup.py install [--prefix=安装路径]
```