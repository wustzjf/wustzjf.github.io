---
layout:     post
title:      python学习-ssh脚本自动登入
date:       2021-01-16
author:     周思进
header-img:	
catalog: false
tags:
    - Python
---

之前项目中需要用到 ssh 端口转发功能，因为需要 ssh 账户和密码进行认证，且是以非交互的方式执行，最后通过使用 expect 库来完成了 ssh 自动登入。

python 同样有类似的库，pexpect。做嵌入式开发，平常可能经常需要 ssh 登入设备，而不同设备 ip 不一样，但账户及密码基本都是一样的，那很适合通过 python 脚本来实现 ssh 自动登入操作。

首先需要按照 pexpect 模块

```
pip3 install pexpect
```

如果安装时提示如下错误

> WARNING: You are using pip version 20.1.1; however, version 20.3.3 is available.
You should consider upgrading via the '/usr/local/bin/python3.6 -m pip install --upgrade pip' command.

则先对 pip 进行升级再安装：

```
pip3 install --upgrade pip
```

安装完成后，编写如下类似脚本执行即可：


```

import pexpect
import sys


def autosshlogin(host, user="pi", passwd="12345"): # 如果不传递用户名和密码，则使用默认值
    ssh = pexpect.spawn('ssh %s@%s'%(user, host))
    try:
        index = ssh.expect(['password:', 'yes/no'], timeout=10) # 期待的字符串可以是列表组合，实际匹配列表中的哪个，就返回对应列表的下标值；如果10s连接不少，则超时返回
        if 0 == index: # 匹配 password，直接发送密码接口
            ssh.sendline(passwd)
        elif 1 == index: # 说明是第一次连接该服务器，需要同意接入
            ssh.sendline("yes\n")
            ssh.expect('password')
            ssh.sendline(passwd)
    except pexpect.TIMEOUT:
        print("connect failed.\n")
        return 

    ssh.expect('$') # 等待登入后的 shell 提示符
    ssh.interact() # 将控制转给用户交互式操作


if __name__ == "__main__":
    argc = len(sys.argv)
    if (argc < 2 or argc > 4):
        print("usage:autosshlogin IP username(default pi) passwd(default 12345)\n")
        sys.exit(1)

    if (argc == 2):
        autosshlogin(sys.argv[1])
    elif (argc == 3):
        autosshlogin(sys.argv[1], sys.argv[2])
    elif (argc == 4):
        autosshlogin(sys.argv[1], sys.argv[2], sys.argv[3])
    else:
        print("usage:autosshlogin IP username(default pi) passwd(default 12345)\n")
        sys.exit(1)
```

后面只要需登入的设备账户和密码都是一样的，则脚本只需传递下 IP 地址即可登入，便捷多了。


