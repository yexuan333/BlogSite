## pip 安装
下载安装程序 https://bootstrap.pypa.io/get-pip.py，保存成为文件 "get-pip.py"

命令行执行./python.exe get-pip.py，之后会看到，该脚本把pip, setuptools, wheel三个东西都装好了
默认安装到了 ".\Lib\site-packages\" 路径

找到文件 "python311._pth",在原有的内容下面添加`.\\Lib\\site-packages\\`