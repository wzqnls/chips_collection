## 1.准备编译环境
```
     yum install zlib-devel bzip2-devel openssl-devel ncurese-devel
```

## 2.下载python3.5安装包
    从官网下载既可，具体存放位置可按个人喜好

## 3.编译安装
```
    tar Jxvf tar Jxvf Python-3.5.2.tar.xz
    cd Python-3.5.2
    ./configure --prefix=/usr/local/python3
    make && make install
```

## 4.给python2更名，相当于备份保存
```
    mv /usr/bin/python /usr/bin/python2.7
```

## 5.为python3和pip设置软连接
```
    ln -s /usr/local/python3/bin/python3.5 /usr/bin/python
    ln -s /usr/local/python3/bin/pip3 /usr/bin/pip
```
若提示文件已存在，则删除相应文件
例如：
```
    rm /usr/bin/python    
```

## 6.检查python以及pip是否安装成功
```
    # 查看相应版本信息既可
    python --version
    pip --version
```

## 7.更新yum配置，防止由于python默认版本改动造成的无法使用
```
    # 修改yum配置文件
    vim /usr/bin/yum
    # 改动文件头
    #!/usr/bin/python2.7

    # 修改urlgrabber-ext-down
    vim /usr/libexec/urlgrabber-ext-down
    # 同上
    #!/usr/bin/python2.7
```

## 8.注意事项
若pip未安装成功，则很有可能是ssl没有安装。返回至1，进行编译环境的安装。
其他问题则注意报错内容，见招拆招。