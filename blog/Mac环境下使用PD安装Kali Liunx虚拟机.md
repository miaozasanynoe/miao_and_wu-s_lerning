## Mac环境下使用PD安装Kali Liunx虚拟机
### 1. 环境前提
    如果你不具有以下环境，那么请先自行配置完环境再继续配置 :
    1. 操作系统：Macos Catalina  

    2. 虚拟机:Parallels Desktop
### 2. 下载Kali Liunx iso镜像
1. 进入官网下载种子文件  

2. 使用迅雷通过种子文件下载iso镜像
### 3. 安装Kali Liunx
1. 打开Paralels Desktop，将iso镜像文件拖入其中，进行安 装（出现文件损坏等提示也继续安装）  

2. 按照安装步骤点next，next。。。。  

3. 设置用户名与密码  

### 4. 安装图形化界面
1. 开机login以后，输入以下命令进行安装  
    ```bash
    apt-get update
    apt-get install x-window-system-core
    apt-get install gnome-core
    ```   

2. 重新启动，进入图形化系统  

### 5. 安装PDtools
1. 点击右上角的黄色惊叹号，点击确定挂载文件  

2. 卸载之前挂载的文件，重新挂载为可执行的文件  

    ```bash
    umount cdrom0
    mount -o exec cdrom0
    ```
3. 安装Pdtools所需的依赖gcc和dbms
    ```bash
    sudo apt-get install gcc
    sudo apt-get install dbms
    ```  

4. 运行install 脚本  

    ```bash
    sudo ./install
    ```
5. 编译错误，新内核导致无法安装Pdtools，凑合着使用吧
