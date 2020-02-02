### Nginx的安装与简单学习
##### 1.安装相关的依赖
```
yum -y install make zlib zlib-devel gcc-c++ libtool  openssl openssl-devel
```
#### 2.下载上传解压
下载nginx（https://nginx.org/download/），上传到服务器，解压
```
tar -zxvf nginx-1.9.9.tar.gz
```
### 3.编译安装nginx
```
cd nginx-1.9.9
./configure --prefix=/usr/local/nginx
make
make install
```
### 4.配置conf文件
```
cd ..
cd nginx/conf
vi nginx.conf
```
修改以下配置
```
server {
        listen	212       ;#监听的端口号
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   /usr/local/html;#资源的根目录
            index  index.html index.htm;#默认主页
        }

        #error_page  404              /404.html;#默认404页面

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;#默认500x错误页面
        location = /50x.html {
            root   html;
        }
        ..........
}
```
### 5.启动nginx
```
cd sbin
./nginx
```
另外一些其他的常用命令：
```
./nginx -s stop 关闭
./nginx -s reload 重新加载配置文件
```

### 6.使用防火墙开启端口号
```
firewall-cmd --add-port=212/tcp --permanent
firewall-cmd --reload
```

### 7.在浏览器中访问 ip地址:212即可浏览网页
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

![image](http://img4.imgtn.bdimg.com/it/u=2503911009,288538861&fm=26&gp=0.jpg)