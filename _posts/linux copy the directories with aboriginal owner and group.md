---
layout: post
title:  "linux copy the directories with aboriginal owner and group"
date:   2024-04-08 23:15:00 +0700
categories: [python, django]
---

python脚本：

```python
import os
import shutil
import pwd
import grp

def copy_dir_structure(src_dir, dest_dir):
    if not os.path.exists(dest_dir):
        os.makedirs(dest_dir)

    for entry in os.scandir(src_dir):
        if entry.is_dir():
            src_path = entry.path
            dest_path = os.path.join(dest_dir, entry.name)

            # 创建子目录
            os.makedirs(dest_path)

            # 获取源目录的owner和group
            owner = pwd.getpwuid(entry.stat().st_uid).pw_name
            group = grp.getgrgid(entry.stat().st_gid).gr_name

            # 设置目标目录的owner和group
            shutil.chown(dest_path, user=owner, group=group)

            # 递归复制子目录结构
            copy_dir_structure(src_path, dest_path)

if __name__ == "__main__":
    src_dir = "/path/to/source/directory"
    dest_dir = "/path/to/destination/directory"

    copy_dir_structure(src_dir, dest_dir)
```

只复制第一层子目录就可以，不用递归复制子目录结构

```python
import os
import shutil
import pwd
import grp

def copy_first_level_dirs(src_dir, dest_dir):
    if not os.path.exists(dest_dir):
        os.makedirs(dest_dir)

    for entry in os.scandir(src_dir):
        if entry.is_dir():
            src_path = entry.path
            dest_path = os.path.join(dest_dir, entry.name)

            # 创建子目录
            os.makedirs(dest_path)

            # 获取源目录的owner和group
            owner = pwd.getpwuid(entry.stat().st_uid).pw_name
            group = grp.getgrgid(entry.stat().st_gid).gr_name

            # 设置目标目录的owner和group
            shutil.chown(dest_path, user=owner, group=group)

if __name__ == "__main__":
    src_dir = "/path/to/source/directory"
    dest_dir = "/path/to/destination/directory"

    copy_first_level_dirs(src_dir, dest_dir)
```

在上面这个脚本的基础上，如果 目标目录已经存在满足要求的子目录则跳过这个子目录

```python
import os
import shutil
import pwd
import grp

def copy_first_level_dirs(src_dir, dest_dir):
    if not os.path.exists(dest_dir):
        os.makedirs(dest_dir)

    for entry in os.scandir(src_dir):
        if entry.is_dir():
            src_path = entry.path
            dest_path = os.path.join(dest_dir, entry.name)

            # 检查目标目录是否已经存在满足要求的子目录
            if os.path.exists(dest_path):
                # 获取源目录和目标目录的owner和group
                src_owner = pwd.getpwuid(entry.stat().st_uid).pw_name
                src_group = grp.getgrgid(entry.stat().st_gid).gr_name
                dest_owner = pwd.getpwuid(os.stat(dest_path).st_uid).pw_name
                dest_group = grp.getgrgid(os.stat(dest_path).st_gid).gr_name

                # 检查owner和group是否相同
                if src_owner == dest_owner and src_group == dest_group:
                    print(f"Skipping {dest_path}, as it already exists with the required owner and group.")
                    continue

            # 创建子目录
            os.makedirs(dest_path)

            # 获取源目录的owner和group
            owner = pwd.getpwuid(entry.stat().st_uid).pw_name
            group = grp.getgrgid(entry.stat().st_gid).gr_name

            # 设置目标目录的owner和group
            shutil.chown(dest_path, user=owner, group=group)

if __name__ == "__main__":
    src_dir = "/path/to/source/directory"
    dest_dir = "/path/to/destination/directory"

    copy_first_level_dirs(src_dir, dest_dir)
```

