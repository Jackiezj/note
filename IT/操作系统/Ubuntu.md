---
title: ubuntu16的使用问题
author: jackiezz
tags:
  - null
top: 1
date: 2019-02-27 14:06:00
categories:
cover_picture:

---

------

title: 007.Ubuntu使用问题
author: jackiezz
tags:

- ubuntu
  top: 1
  date: 2018-06-29 18:51:42
  categories: 操作系统
  cover_picture: images/ubuntu.jpg

------

# Ubuntu使用问题

## 一. 初始化Ubuntu

### 1.1 系统清理

- 更新补丁

  ```bash
  sudo apt-get update 
  sudo apt-get upgrade
  ```

- 卸载LibreOffice

  ```bash
  sudo apt-get remove libreoffice-common
  ```

- 删除Amazon

  ```bash
  sudo apt-get remove unity-webapps-common 
  ```

- 删除不常用的软件

  ```bash
  sudo apt remove thunderbird totem rhythmbox empathy brasero simple-scan gnome-mahjongg aisleriot --purge
  sudo apt remove gnome-mines cheese transmission-common gnome-orca webbrowser-app gnome-sudoku  landscape-client-ui-install  --purge
  sudo apt remove onboard deja-dup --purge
  ```

### 1.2 主题美化

- 安装unity-tweak-tool工具

  ```bash
  sudo apt-get install unity-tweak-tool 
  ```

- 安装Flatabulous主题

  ```bash
  sudo add-apt-repository ppa:noobslab/themes
  sudo apt-get update
  sudo apt-get install flatabulous-theme
  ```

- 使用主题

  ```bash
  unity-tweak-tool
  ```

  - 选择主题修改为: Flatabulous

  - 选择图标修改为: Ubuntu-mono-dark

### 1.3 常用的操作

- `Shift + +` 放大终端窗口字体
- `shift + -` 缩小终端窗口字体
- `tab`自动补全
- `上`和`下`键头调用历史命令
- `.`为当前目录`..`为上一级目录`/`为根目录`~`为家目录
- `Ctrl+c`取消命令

### 1.4 修复系统vi

- 修复vi

  ```bash
  sudo apt-get remove vim-common --purge
  sudo apt-get install vim
  sudo vi /etc/vim/vimrc.tiny
      set nocompatible
  ```

### 1.5 更换软件源

- 更换为aliyum

  aliyum :  https://opsx.alibaba.com/mirror

  ```bash
  cd /etc/apt/
  sudo cp sources.list sources.list.backup
  sudo vi sources.list
  # 内容: 点击上面网站, 选择Ubuntu --> 点击帮助 即可看到配置信息, 进行全部替换即可
  sudo apt update
  sudo apt upgrade
  ```

  > sudo apt-get update :  会重新建立这些资料，所以不必担心删除后会出问题；
  > sudo apt-get upgrade :  会更新已安装的软件的明细，根据软件的明细更新软件到最新版。

### 1.6 键盘键位互换

- Caps与Esc互换, ctrl和win互换

  ```bash
  sudo vim /etc/default/keyboard
  	XKBOPTIONS="caps:swapescape,ctrl:swap_lwin_lctl,ctrl:swap_rwin_rctl"
  	cat /usr/share/X11/xkb/rules/xorg.lst  # 查看XKBOPTIONS的具体修改项
  sudo dpkg-reconfigure keyboard-configuration  # 加载使配置生效
  ```

### 1.7 安装rime输入法

- 安装rime

  ```bash
  sudo apt install ibus-rime
  sudo apt-get install librime-data-wubi
  ```

- rime的设置

  ```python
  vi ~/.config/ibus/rime/default.yaml  # 修改配置
      menu:  # 每页显示个数
        page_size: 10
      switcher:
        caption:
        hotkeys:  # 调出输入法切换面板及相关设置
          - Control+grave
          - Control+Shift+grave
          - F4
      schema_list:  # 设置调出输入法的候选项
        - schema: luna_pinyin
        - schema: cangjie5
        - schema: wubi86
  
      ascii_composer:
        good_old_caps_lock: true
        switch_key:  # 修改功能键
          Shift_L: commit_code  # 提交字符串并转为英文
          Shift_R: commit_text  # 提交首选词并转为英文
          Control_L: noop
          Control_R: noop  # 什么也不做
          Caps_Lock: clear  # 清除拼音并转为英文大写，ESC清除拼音
          Eisu_toggle: clear
  ```

- 词典备份与恢复

  ```bash
  # 备份
  vi ~/.config/ibus/rime/installation.yaml
      # 手动点击同步或自动同步
      installation_id: "ju"
      sync_dir: '/home/jackiezz/soft/rime'
      
  # 恢复
  /usr/bin/rime_dict_manager
  	# 回车可显示操作方法
  ```

## 二. 安装软件

### 2.1 apt安装软件

- 安装

  ```bash
  sudo apt install soft  # 安装软件包
  sudo apt --purge remove  # 卸载软件包
  sudo apt upgrade  # 更新软件包
  ```

### 2.2 deb软件包

- 安装与卸载

  ```bash
  # 安装
  sudo dpkg -i xxx.deb
  # 卸载
  sudo dpkg -l | grep xxx  # 显示已经安装的软件
  sudo dpkg -r xxx
  ```

  > 如果有依赖关系错误 --> 执行 sudo apt install -f
  >
  > “dpkg ”是“Debian Packager ”的简写。Ubuntu是源自“Debian”的“Linux ”发行版, 所以使用 “dpkg”

### 2.3 rpm软件包

- 安装

  > Ubuntu的软件包格式是deb，如果要安装rpm的包，则要先用alien把rpm转换成deb。

  ```bash
  sudo alien xxxx.rpm
  sudo dpkg -i xxxx.deb
  ```

### 2.4 .gar.gz包

## 三. python解释器及环境

### 3.1 虚拟环境

- **安装虚拟环境**

  ```bash
  sudo apt install virtualenv # 安装虚拟环境
  sudo apt install virtualenvwrapper # 安装虚拟环境扩展包
  mkdir $HOME/.virtualenvs
  vi ~/.bashrc
  	export WORKON_HOME=$HOME/.virtualenvs
  	source /usr/share/virtualenvwrapper/virtualenvwrapper.sh
  # 使配置文件生效。
  source .bashrc
  # 如果使用zsh则vi .zshrc然后source .zshrc
  ```

- **创建虚拟环境**

  ```bash
  mkvirtualenv -p python3 vir_django  # 创建python3虚拟环境vir_django
  ```

- **使用虚拟环境**

  ```bash
  workon 两次tab键  # 查看所有虚拟环境
  ```

- **退出与删除虚拟环境**

  ```bash
  deactivate  # 退出虚拟环境
  rmvirtualenv vir_django  # 删除虚拟环境
  ```

- **包操作**

  ```bash
  # python3环境用pip3， python2环境用pip2
  # 工具包的位置
  ~/.virtualenvs/vir_django/lib/python3.5/site-packages
  # 安装包
  sudo pip3 install 包名  # 安装python包（需要联网,且安装在非虚拟环境中）
  pip install 包名  # 安装在虚拟环境中(如果环境为python3, 则pip == pip3)
  # 查看包
  pip list  # 查看已安装的python包
  # 从文件中安装包
  pip freeze > requirements.txt  # 将包环境写入该txt文件中
  pip install -r requirements.txt  # 安装txt中的包环境
  ```

## 四. vmware虚拟机的使用

- 下载安装

  - 下载地址

    https://www.vmware.com/cn/products/workstation-pro/workstation-pro-evaluation.html

  - 安装

    ```bash
    sudo chmod +x VMware.bundle
    sudo ./VMware.bundle
    ```

    注册码

    > YG5H2-ANZ0H-M8ERY-TXZZZ-YKRV8
    >
    > UG5J2-0ME12-M89WY-NPWXX-WQH88
    >
    > UA5DR-2ZD4H-089FY-6YQ5T-YPRX6
    >
    > GA590-86Y05-4806Y-X4PEE-ZV8E0
    >
    > ZF582-0NW5N-H8D2P-0XZEE-Z22VA
    >
    > YA18K-0WY8P-H85DY-L4NZG-X7RAD

  - 卸载

    ```bash
    sudo vmware-installer -u vmware-workstation
    ```


## 五. Ubuntu 16.04 Bug解决

### 5.1 关机卡死在关机界面

- 卡在mysql

  ```bash
  sudo vim /etc/systemd/system/multi-user.target.wants/mysql.service
  	TimeoutSec=5
  ```

- 卡死在redis

  ```bash
  sudo vim /etc/init.d/redis_6379
  
  PASSWORD="1234qwer"
  
  $CLIEXEC -p $REDISPORT -a $PASSWORD shutdown
  ```

- 设置超出时间

  ```bash
   sudo vi /etc/systemd/system.conf
   	DefaultTimeoutStartSec=10s
  	DefaultTimeoutStopSec=10s
  ```

### 5.2 网易云音乐root权限才能打开的问题

- 解决

  ```bash
  sudo vi /usr/share/applications/netease-cloud-music.desktop
  	修改: Exec=sudo netease-cloud-music --no-sandbox %U
  # 如果不行, 再执行下面
  sudo vi /etc/sudoers
  	追加: YOURNAME ALL = NOPASSWD: /usr/bin/netease-cloud-music  #YOURNAME为你的用户名
  ```

## 六. Ubuntu设置问题

### 6.1 Ubuntu调节屏幕亮度

- 添加fluxgui

    ```bash
    sudo apt install redshift
    ```


## 七. Ubuntu开发中的设置

### 7.1 生成添加SSH key

- 本地设置ssh-key

  ```bash
  ssh-keygen -t rsa -C "xxxxx@xxxxx.com" 
  cat ~/.ssh/id_rsa.pub
  # 添加到远程相应位置
  ssh -T git@gitee.com
  ```

### 7.2 不占用终端运行程序

- 命令

  ```bash
  nohup 程序 &
  ```

## 八. Ubuntu常用软件

### 8.1 sublime_text_3

- 安装

  官网下载

- 在启动器中添加快捷方式

  ```bash
  cd /usr/share/applications
  sudo vi sublime.desktop
  ```

  ```bash
  [Desktop Entry]
  Encoding=UTF-8
  Version=3.2
  Name=sublime_text_3
  # Only KDE 4 seems to use GenericName, so we reuse the KDE strings.
  # From Ubuntu's language-pack-kde-XX-base packages, version 9.04-20090413.
  GenericName=Text Editor
  
  Exec=/opt/sublime_text_3/sublime_text
  Terminal=false
  Icon=/opt/sublime_text_3/Icon/256x256/sublime-text.png
  Type=Application
  Categories=TextEditor;IDE;Development
  X-Ayatana-Desktop-Shortcuts=NewWindow
  
  [NewWindow Shortcut Group]
  Name=New Window
  Exec=/opt/sublime_text_3/sublime_text
  TargetEnvironment=Unity
  ```

- 破解

  ```bash
  sudo vi /etc/hosts
  # 追加
  	127.0.0.1       www.sublimetext.com
  	127.0.0.1       license.sublimehq.com
  # 软件中输入注册码
  ZYNGA INC.
  50 User License
  EA7E-811825
  927BA117 84C9300F 4A0CCBC4 34A56B44
  985E4562 59F2B63B CCCFF92F 0E646B83
  0FD6487D 1507AE29 9CC4F9F5 0A6F32E3
  0343D868 C18E2CD5 27641A71 25475648
  309705B3 E468DDC4 1B766A18 7952D28C
  E627DDBA 960A2153 69A2D98A C87C0607
  45DC6049 8C04EC29 D18DFA40 442C680B
  
  1342224D 44D90641 33A3B9F2 46AADB8F
  ```

  

- 插件安装

  - 安装方法

    ```
    Ctrl+shift+p ==> package control ==> install package ==> Markdown etc
    ```

  - 常用插件

    ```
    Emmet（原名 Zen Coding）——快速编写html/css的方法
    ConvertToUTF-8 ——解决乱码问题
    Alignment——代码对齐插件
    AutoPrefixer ——css3前缀自动添加插件
    JS Fromat —— 格式化JS
    Minifier——JS压缩插件
    Sublime CodeIntel——代码自动提示
    SideBarEnhancements——侧边栏插件
    Keymaps ——快捷键查找等
    CSScomb——CSS属性排序
    
    作者：FocusOn_
    链接：https://www.jianshu.com/p/edeee16cdea1
    来源：简书
    简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。
    ```

    































































































































