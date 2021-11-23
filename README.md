# debugself
![www.debugself.como](https://www.debugself.com)的blog源码

## 首次初始化 
先安装Node.js和git，然后执行如下命令
```
npm install
git clone https://github.com/gdbself/jacman.git themes/jacman
```
修改themes/jacman/\_config.yml,支持www.debugself.com的百度统计
```
baidu_tongji:
  enable: true
  sitecode: 34f485669ac055e5402891996130d3ac ## e.g. e6d1f421bbc9962127a50488f9ed37d1 your baidu tongji site code 
```
## 编写.md文件
markdown文件放到source/\_post目录

## 生成静态文件
```
npx hexo g
```

## 启动hexo的服务器
```
npx hexo server -p 8080
```

## ngxin配置文件见other
