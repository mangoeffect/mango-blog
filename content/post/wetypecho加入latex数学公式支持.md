---
title: "wetypecho加入latex数学公式支持"
categories: [ "网站" ]
tags: [ "typecho","mathjax","wetypecho","latex" ]
draft: false
slug: "wetyoeho-support-latex-render"
date: "2020-03-01 22:46:00"
---

本教程需要的准备条件与安装wetypecho的条件相同，在往下阅读之前你需要准备好：

- 已经使用typecho搭建博客，并且博客**域名已经备案**
- 博客开启**https**访问

托管主机推荐腾讯云，新用户优惠力度还是比较大的，本站使用的就是腾讯云。优惠链接
[![【腾讯云】云产品限时秒杀，爆款1核2G云服务器，首年74元](https://mangoroom.cn/usr/uploads/2021/09/2788699968.jpg)](https://mangoroom.cn/go/tencentcloud1/)

**小程序渲染效果：**

![封面.jpg][1]

## 部署latex解析服务

本文方法让微信小程序支持latex的原理是通过部署一个后台的解析api实现的，通过将公式语句数据发送到后台，而后生成图片的编码数据，最后是通过在小程序前端界面以矢量图片的方式渲染。解析api的代码仓库在GitHub上，仓库地址为：[https://github.com/sbfkcel/markdown-server](https://github.com/sbfkcel/markdown-server)。安装部署步骤非常简单

### clone 项目

```bash
git clone https://github.com/sbfkcel/markdown-server
```
### 安装依赖

```bash
npm install 或 yarn
```
### 启动服务

```bash
node index.js
```

但直接这么部署不太方便，这样一来不好设置域名解析和https，下面我介绍在宝塔面板的部署方法。

### （1）安装PM2管理器

PM2是一个node项目的进程管理器，在宝塔软件商店搜索安装即可安装。
![PM2_1.png][2]

### （2）新建网站

在宝塔面板新建一个网站，设置好**域名**，设置好域名解析。node类型项目并不需要php环境，选择纯**静态**类型的网站。

![PM2_2.png][3]

### （3）下载markdown-server项目代码覆盖网站目录

进入到新建网站目录，删除自动生成的文件，下载markdown-server项目代码到网站根目录。此处可以使用宝塔的远程下载功能下载，这样就无需下载到本地再上传。
![PM2_3.png][4]
![PM2_4.png][5]

### （4）安装node依赖

打开宝塔SSH终端，或者自己远程SSH连接服务器。然后cd到网站根目录下，执行命令安装node依赖。

```bash
npm install 或 yarn
```

![PM2_5.png][6]
### （5）设置PM2管理器

  选择项目所在目录：第一步新建网站的网站根目录（默认与域名同名）
  启动文件名：index.js
  项目名称：自行设置（如latex-api)
  添加

![PM2_6.png][7]
### （6）修改网站配置文件
然后复制端口号，到网站配置文件中修改，在配置文件中添加

```nginx
    location / {
		proxy_pass http://127.0.0.1:8001;#端口号与PM2管理器出一致
 	}
```
![PM2_7.png][8]

### （7）申请SSL证书

![PM2_8.png][9]
在网站设置中免费申请并部署证书，推荐Let's Encrypt的，比较方便省事。部署完后将强制https打开。到这一步，用浏览器访问新建网站的域名将出现以下页面

```
Please pass LaTeX formula via `tex` parameter or `Yuml` expression using `yuml` parameter.
```

如果出现以上提示，说明已经成功部署latex的解析api后台服务。

## 修改wetypecho

部署好latex解析服务后，我们还需要通过修改小程序的前端渲染来支持latex公式的正确显示。前端界面解析和渲染markdown使用的是towxml库，这是一个将markdown数据转为微信小程序的wxml来渲染。towxml和上面的api解析都是来自同一个作者：sbfkcel大大，要完成小程序渲染latex数学公式需要两者配合。所以需要将towxml应用到小程序中，幸运的是wetypeho的作者在开发的时候使用的也是towxml，只不过旧版本的towxml，所以接下来的工作是只需要将程序中的towxml更新为新版本的towxml即可。

### （1）构建towxml

克隆项目到本地

```bash
git clone https://github.com/sbfkcel/towxml.git
```

安装构建依赖

```bash
npm install 或 yarn
```

编辑配置文件towxml/config.js
根据自行要求，仅保留你需要的功能即可（配置中有详细注释）
```nginx
 // 数学公式解析API
    latex:{
        api:'http://towxml.vvadd.com/?tex'  //替换为第一步新建的网站域名
    },

    // yuml图解析APPI
    yuml:{
        api:'http://towxml.vvadd.com/?yuml' //替换为第一步新建的网站域名
    },
```

运行 npm run build 或 yarn run build即可,构建完成，会在界面生成towxml文件夹。

### （2）将构建得到的towxml文件考到wetypecho目录，替换掉原来的towxml(先删除再拷贝进来)。

### （3）修改wetypecho

####  app.js:

towxml3.0的使用方式有所改变，导入的方式修改为

```js
//const Towxml = require('/towxml/parse');
App({
  Data:{
    userInfo: null,
    zaned: false
  },
  // 引入`towxml3.0`解析方法
  towxml: require('/towxml/index'),
})
```

#### detail.js

towxml3.0的渲染api使用也有所变化，在getdetails函数中渲染方式修改如下，注释掉部分为2.0渲染方式。

```js
//获取文章详细
  getdetails(cid) {
    var that = this;
    Net.request({
      url: API.GetPostsbyCID(cid),
      success: function(res) {
        var datas = res.data.data;
        var parsed_item = API.ParseItem(datas[0]);
        var data = parsed_item.text.replace(/!!!/g,"");
        //let data_parse = app.towxml.toJson(data,'markdown');
        let data_parse = app.towxml(data, 'markdown');
        that.setData({
          content: data_parse,
          item: parsed_item,
        })
        //发布时间
        that.data.createdtime = API.getcreatedtime(parsed_item.created);
        that.setData({
          createdtime: that.data.createdtime
        })
        //获取相关文章
        that.fetchpostbymid(that.data.item.mid)
      }
    })
```

#### detail.wxml

detail.wxml文件约33行，修改为

```html
<view class="container">
  <towxml nodes="{{content}}"/>
</view>
```

同理，参考detail.js和detail.wxml文件的方法，去修改about页面的js和wxml文件。修改的地方都是一致的。完成后的效果可以微信扫描下方二维码查看：

![渲染效果.jpg][10]


## references

[【1】安装WeTypecho详细步骤](https://2012.pro/index.php/20180811/cid=77.html)

[【2】微信小程序HTML、Markdown渲染库-Github](https://github.com/sbfkcel/towxml)

[【3】Markdown-server-Github](https://github.com/sbfkcel/markdown-server)

---

本文由芒果浩明发布，转载请注明出处。
本文链接：https://mangoroom.cn/website/wetyoeho-support-latex-render.html


  [1]: https://mangoroom.cn/usr/uploads/2020/03/1962872533.jpg
  [2]: https://mangoroom.cn/usr/uploads/2020/03/1877059212.png
  [3]: https://mangoroom.cn/usr/uploads/2020/03/2817598091.png
  [4]: https://mangoroom.cn/usr/uploads/2020/03/2654685755.png
  [5]: https://mangoroom.cn/usr/uploads/2020/03/3444256110.png
  [6]: https://mangoroom.cn/usr/uploads/2020/03/846572844.png
  [7]: https://mangoroom.cn/usr/uploads/2020/03/4005287692.png
  [8]: https://mangoroom.cn/usr/uploads/2020/03/2612417864.png
  [9]: https://mangoroom.cn/usr/uploads/2020/03/2479604120.png
  [10]: https://mangoroom.cn/usr/uploads/2020/03/1398693449.jpg