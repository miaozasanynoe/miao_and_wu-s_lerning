### Nginx的安装与简单学习
##### 1.安装相关的依赖
```
yum -y install make zlib zlib-devel gcc-c++ libtool  openssl openssl-devel
```
#### 2.下载

![image](http://img4.imgtn.bdimg.com/it/u=2503911009,288538861&fm=26&gp=0.jpg)








#### tips 接口设计:
> login接口：
```
param{
    username:xxx
    pasword:xxx
}

back{
    code:1|0|-1
    msg:"succes"|"failed"
    date:{
        avatar:"xxxx.png"
        password:xx
        age:19
    }
}