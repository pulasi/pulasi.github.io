---
layout: post
title:  python复制第一层子目录并保留权限
date: 2024-4-09
categories: blog
tags: [python,linux]
description: python复制第一层子目录并保留权限。
---


```python
import os
import shutil

def copy_permissions(src_dir, dest_dir):
    for root, dirs, files in os.walk(src_dir):
        if root == src_dir:
            for dir in dirs:
                src_path = os.path.join(root, dir)
                dest_path = os.path.join(dest_dir, dir)
                
                # 获取源目录的owner和group
                stat_info = os.stat(src_path)
                uid = stat_info.st_uid
                gid = stat_info.st_gid
                
                # 设置目标目录的owner和group
                os.chown(dest_path, uid, gid)
            break

# 示例用法
src_dir = "/path/to/source/directory"
dest_dir = "/path/to/destination/directory"
copy_permissions(src_dir, dest_dir)

python2
```

```python
import os
import shutil

def copy_permissions(src_dir, dest_dir):
    for root, dirs, files in os.walk(src_dir):
        for dir in dirs:
            src_path = os.path.join(root, dir)
            dest_path = os.path.join(dest_dir, os.path.relpath(src_path, src_dir))
            
            # 获取源目录的owner和group
            stat_info = os.stat(src_path)
            uid = stat_info.st_uid
            gid = stat_info.st_gid
            
            # 设置目标目录的owner和group
            os.chown(dest_path, uid, gid)

# 示例用法
src_dir = "/path/to/source/directory"
dest_dir = "/path/to/destination/directory"
copy_permissions(src_dir, dest_dir)

python3
```

这个代码会遍历源目录（src\_dir）中的所有子目录，并获取它们的owner和group。然后，它会在目标目录（dest\_dir）中找到对应的子目录，并设置相同的owner和group。

请注意，这个代码需要以root权限运行，以便能够获取和设置目录的owner和group。在运行此代码之前，请确保您了解其功能和影响，并确保已经备份了相关数据。
