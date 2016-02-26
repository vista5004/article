#使用AngularJS, Node.js and MongoDB创建一个TV节目跟踪器

<p>这个教程将会给你展示如何用Express创建一个REST API，用护照进行身份验证和登录过程，利用 Mongoose在MongoDB增加和读取数据。前端将会用 AngularJS 和 Bootstrap SASS建立。最后一步式用gulp来优化静态资源。</p>

![Alt text](http://sahatyalkabov.com/images/blog/tvshow-tracker-32.png)
[源码](https://github.com/sahat/tvshow-tracker) 
####August 18, 2014更新，GitHub repository代码已经更新，下面是一些改变的简要概述<br>
<ul>
<li>去除method-override, cookie-parser and express-session模块
<li>JSON Web Token认证代替了基于cookies的方法。
<li>使用Google登录
<li>使用Facebook登录
<li>使用ngAnnotate代替ngMin为AnguarJS依赖进行注释
<li>一个新的基于Google's Material Design的警告通知
<li>普通UI的更新和升级
<li>基于ng-animate的页面切换
<li>加入一个基于Karma配置文件的单元测试
<li>类似于Stripe 和 Dropbox的登录页面的密码强度指令
<li>在登录界面Email已经采用指令提供实时反馈
<li>使用promise代替$resource.save方法的回调函数
<li>Icon字体
<li>许多代码已经重构和清理
</ul>
在开始程序之前，确保你已经安装了以下：<br>
<ul>
<li>Node.js(https://nodejs.org/en/)
<li>MongoDB(http://www.mongodb.org/downloads)
<li>express-generator(https://github.com/expressjs/generator)
</ul>
##第一步，新建一个新的Express文件
在安装完[express-generator](https://github.com/expressjs/generator)后我们可以使用<code>express</code>命令新建一个小的Express应用。<p>
运行<code>express showtrackr</code>创建一个新的Express项目，showtrackr就是我们今天要创造这个项目的名字。<p>
![Alt text](http://sahatyalkabov.com/images/blog/tvshow-tracker-1.png)
进入showtrackr文件夹，然后运行<code>npm install</code>命令。
![Alt text](http://sahatyalkabov.com/images/blog/tvshow-tracker-2.png)
删除用不到的views、routes和bin文件夹，然后把<code>app.js</code>改成<code>server.js</code>,我们会写创建另一个app.js来启动Angularjs程序<p>
![Alt text](http://sahatyalkabov.com/images/blog/tvshow-tracker-3.png)
用下面的代码来代替，server.js中的代码
```
var express = require('express');
var path = require('path');
var logger = require('morgan');
var cookieParser = require('cookie-parser');
var bodyParser = require('body-parser');

var app = express();

app.set('port', process.env.PORT || 3000);
app.use(logger('dev'));
app.use(bodyParser.json());
app.use(bodyParser.urlencoded());
app.use(cookieParser());
app.use(express.static(path.join(__dirname, 'public')));
app.listen(app.get('port'), function() {
  console.log('Express server listening on port ' + app.get('port'));
});
```
##第二步，启动AngularJS应用
![Alt text](http://sahatyalkabov.com/images/blog/tvshow-tracker-4.png)
复制所有的glyphicons文件从assets/fonts/bootstrap到 public/fonts文件夹，并且把assets/stylesheets的所有文件拷入public/stylesheets/bootstrap文件夹<br>
2014年12月4日更新：增加SASS文件路径<br>
![Alt text](http://sahatyalkabov.com/images/blog/tvshow-tracker-5.png)
下载这个图片[http://i.imgur.com/A38jRib.png]，并且放在public文件夹，你不是很需要它，但是这是一个很棒的手法。<br>

