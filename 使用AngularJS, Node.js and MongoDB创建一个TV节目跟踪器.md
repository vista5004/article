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
下载这个[图标](http://i.imgur.com/A38jRib.png)，并且放在public文件夹，你不是很需要它，但是这是一个很棒的手法。<br>
<br>
你仍然需要下载下面的script文件，并他们放到public/vendor文件夹。<br>
<ul>
<li>angular.js(https://code.angularjs.org/1.3.5/)
<li>angular-strap.js(https://code.angularjs.org/1.3.5/)
<li>angular-strap.tpl.js(https://code.angularjs.org/1.3.5/)
<li>angular-message.js(https://code.angularjs.org/1.3.5/)
<li>angular-resource.js(https://code.angularjs.org/1.3.5/)
<li>angular-route.js(https://code.angularjs.org/1.3.5/)
<li>angular-cookies.js(https://code.angularjs.org/1.3.5/)
<li>moment.min.js(http://momentjs.com/)
</ul>
![Alt text](http://sahatyalkabov.com/images/blog/tvshow-tracker-6.png)
在publick文件夹中创建index.html，并且输入以下内容<p>
```
<!DOCTYPE html>
<html ng-app="MyApp">
<head>
  <base href="/">
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>ShowTrackr</title>
  <link rel="icon" type="image/png" href="favicon.png"/>
  <link href="stylesheets/style.css" rel="stylesheet">
</head>
<body>

<div ng-view></div>

<script src="vendor/angular.js"></script>
<script src="vendor/angular-strap.js"></script>
<script src="vendor/angular-strap.tpl.js"></script>
<script src="vendor/angular-messages.js"></script>
<script src="vendor/angular-resource.js"></script>
<script src="vendor/angular-route.js"></script>
<script src="vendor/angular-cookies.js"></script>
<script src="vendor/moment.min.js"></script>
</body>
</html>
```
在第二行<code>ng-app</code>认为这个是AngularJS应用的根元素。<code><base href="/"></code>是AngularJS history API所必须的。这会给我们一个一个干净的没有#号的URL。第14行的<code>ng-view</code>是一个指令，包括了渲染过当前路由的模板。每一次路由变动，这个包含的视图会根据路由服务的配置快速的变更视图。<p>

这和Ember.js中的outlet(http://emberjs.com/api/classes/Ember.Handlebars.helpers.html#method_outlet)很像<p>
创建一个<code></code>在别的脚本文件后加入index.html<p>
```
<script src="app.js"></script>
```
现在在<code>app.js</code>加入如下代码就能启动了。<br>
```
angular.module('MyApp',['ngCookieds','ngResource', 'ngMessages', 'ngRoute', 'mgcrea.ngStrap'])
  .config(function(){
  })
```
我们加入 [AngularStrap Navbar](http://mgcrea.github.io/angular-strap/#/page-one#navbars),把它放在<code>body</code>标签后面。
```
<div class="navbar navbar-default navbar-static-top"
     role="navigation" bs-navbar>
  <div class="navbar-header">
    <a class="navbar-brand" href="/">
      <span class="glyphicon glyphicon-film"></span>
      Show<strong>Trackr</strong></a>
  </div>
  <ul class="nav navbar-nav">
    <li data-match-route="/$"><a href="/">Home</a></li>
    <li data-match-route="/add"><a href="/add">Add</a></li>
  </ul>
  <ul class="nav navbar-nav pull-right" ng-if="!currentUser">
    <li data-match-route="/login"><a href="/login">Login</a></li>
    <li data-match-route="/signup"><a href="/signup">Sign up</a></li>
  </ul>
  <ul class="nav navbar-nav pull-right" ng-if="currentUser">
    <li class="navbar-text" ng-bind="currentUser.email"></li>
    <li><a href="javascript:void(0)" ng-click="logout()">Logout</a></li>
  </ul>
</div>
```
我们使用[ AngularStrap Navbar](http://mgcrea.github.io/angular-strap/#/page-one#navbars)代替[Bootstrap Navbar](http://getbootstrap.com/components/#navbar)的原因就一个，当改变路由的时候，<code>active</code>类会自动应用到<code>li</code>标签。加上你得到的许多非常酷的指令和AngularJS整合在一起，比如Alert，Typeahead, Tooltip等等指令<p>
你可以尝试运行一个APP，你会发现没有任何错误，但是没有出现Navbar，因为我们还没有包括进来bootstrap3的样式文件。我们将会使用gulp来编译sass文件。<p>

直接安装glup和glup插件<p>
```
// Step 1: Install gulp globally
sudo npm install -g gulp

// Step 2: Install gulp in your project
npm install --save-dev gulp gulp-sass gulp-plumber
```
2014年八月升级：你可以全局安装许多NPM模块（使用<code>g</code>标志）代替命令行路径，如果你像步骤2一样下载许多当地模块。你不得不在这个文件夹或者它的子目录内内运行<code>npm install</code>。<p>
通过<code>--save-dev</code>将会在<code>package.json</code>中添加依赖。<p>
![Alt text](http://sahatyalkabov.com/images/blog/tvshow-tracker-7.png)
在项目文件夹中创建一个新的<code>gulpfile.js</code>文件。<p>
```
var gulp = require('gulp');
var sass = require('gulp-sass');
var plumber = require('gulp-plumber');

gulp.task('sass', function() {
  gulp.src('public/stylesheets/style.scss')
    .pipe(plumber())
    .pipe(sass())
    .pipe(gulp.dest('public/stylesheets'));
});

gulp.task('watch', function() {
  gulp.watch('public/stylesheets/*.scss', ['sass']);
});

gulp.task('default', ['sass', 'watch']);
```
![Alt text](http://sahatyalkabov.com/images/blog/tvshow-tracker-8.png)
当你在终端执行<code>gulp</code>的时候，最后一行指定gulp来运行。现在只需要编辑sass样式文件监听文件变化，自动重新编译文件。你可能会好奇[ gulp-plumber](https://github.com/floatdrop/gulp-plumber)。它能够提供gulp管道当其他插件发生错误的时候。换句话说，当在sass文件中发生语法错误的时候，gulp监听器不会发生崩溃，在gulp工作流中不会出现废话。<p>
![Alt text](http://sahatyalkabov.com/images/blog/tvshow-tracker-9.png)
在 public/stylesheets文件夹中创建<code>style.scss</code><p>
```
@import url(http://fonts.googleapis.com/css?family=Open+Sans:300italic,400italic,600italic,700italic,400,300,600,700);

$icon-font-path: '../fonts/';
$body-bg: #e4e7ec;

$font-family-base: 'Open Sans', sans-serif;
$headings-color: #111;
$headings-font-family: Avenir, sans-serif;
$headings-font-weight: bold;

$brand-success: #22ae5f;
$brand-primary: #1d7cf4;
$brand-danger: #b30015;
$brand-warning: #ffd66a;

$text-muted: #90939a;
$link-color: #000;

$navbar-default-link-active-bg: #f7f7f7;
$navbar-default-link-color: #848484;
$navbar-default-bg: #fff;
$navbar-default-border: #e3e9ec;

$navbar-default-brand-color: #333;
$navbar-default-brand-hover-color: #ffe939;
$navbar-default-brand-hover-bg: #333;

$btn-success-bg: $brand-success;
$btn-success-border: darken($btn-success-bg, 3%);
$btn-primary-bg: $brand-primary;
$btn-primary-border: darken($btn-primary-bg, 3%);

$jumbotron-padding: 16px;
$jumbotron-bg: #f4f6f8;

$alert-border-radius: 0;
$input-border-radius: 0;

$alert-success-text: #fff;
$alert-success-bg: #60c060;
$alert-success-border: darken($alert-success-bg, 3%);

$alert-danger-text: #fff;
$alert-danger-bg: $brand-danger;
$alert-danger-border: darken($alert-danger-bg, 3%);

$alert-info-bg: #e5f7fd;
$alert-info-border: #bcf8f3;
$alert-info-text: #25484e;

@import 'bootstrap/bootstrap';

body {
  padding-bottom: 20px;
}

em {
  font-style: normal;
  text-decoration: underline;
}

.alphabet {
  cursor: pointer;
  font-size: 22px;
  text-align: center;

  li {
    display: inline-block;
    padding-left: 5px;
    padding-right: 5px;

    &:hover {
      color: $brand-primary;
    }
  }
}

.genres {
  cursor: pointer;

  li {
    margin-right: 5px;
    @extend .label;
    @extend .label-default;

    &:active {
      box-shadow: inset 0 3px 5px rgba(0, 0, 0, 0.250);
    }
  }
}

.jumbotron {
  margin-top: -20px;
  border-bottom: 1px solid #dae2e4;
}

.media-object {
  max-width: 200px;
  margin-bottom: 10px;
}

.episode {
  border-left: 5px solid #111;
  padding-left: 10px;
}

.alert {
  box-shadow: 0 0px 5px rgba(0, 0, 0, 0.3);
}

.alert.top-right {
  position: fixed;
  top: 50px;
  right: 0;
  margin: 20px;
  z-index: 1050;
  outline: none;

  .close {
    padding-left: 10px
  }
}

.btn {
  border-radius: 2px;
}

.center-form {
  width: 330px;
  margin: 10% auto;

  input {
    border-radius: 0;
  }
}

.search {
  color: #4f4f4f;
  font-weight: 300;
  font-size: 1.5em;
  padding: 7px;
  margin-top: -10px;
  border: 0;
  background-color: transparent;
  outline: none;
  -webkit-appearance: none;

  &:focus {
    -webkit-transition: all .4s ease;
    transition: all .4s ease;
  }
}

.panel {
  border-color: #cfd9D7;
  border-radius: 2px;
  box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.1);
  -webkit-box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.1);
}

.panel-default > .panel-heading {
  color: #444;
  border-color: #cfd9db;
  font-weight: bold;
  font-size: 85%;
  text-transform: uppercase;
  background-color: #f6f6f6;
}

.label {
  display: inline-block;
  margin-bottom: 5px;
  padding: 4px 8px;
  border: 0;
  border-radius: 3px;
  font-size: 12px;
  transition: 0.1s all;
  -webkit-font-smoothing: antialiased;
}

.label-default {
  background-color: #e4e7ec;
  text-shadow: 0 1px 0 rgba(255, 255, 255, 0.7);
  color: #90939a;

  &:hover {
    background-color: #90939a;
    color: #f4f6f8;
    text-shadow: 0 -1px 0 rgba(0, 0, 0, 0.2);
  }
}

.navbar {
  box-shadow: 0 3px 2px -3px rgba(0, 0, 0, 0.1);
}

.navbar-header {
  float: left;
  padding-left: 15px;

}

.navbar-brand {
  background-color: #ffe939;
  transition: 0.25s all;
  margin-left: -15px;
}

.navbar-nav {
  float: left;
  margin: 0;

  > li {
    float: left;

    > a {
      padding: 15px;
    }
  }
}
```
2014年6月8日更新<br>
在项目文件夹中命令行运行<code>gulp</code>后浏览器会刷新。<p>
我通常有一个<code>node server.js</code>服务器来运行一个终端页，<code>mongod</code>在另外一个终端页，<code>gulp</code>在第三个终端页，最后一个终端页是用来一些其他的命令，比如<code>git add</code><code>git commit</code>。<p>
![Alt text](http://sahatyalkabov.com/images/blog/tvshow-tracker-10.png)
如果你对bootstrap不陌生的话，在<code>style.css</code>中的所有东西都应该是简单的。这里只有一些简单的普通类，为了看上去更好看，对bootstrap一些核心类的重写。<p>
####AngularJS路由和模板
回到<code>app.js</code>把下面这行加到config方法里面，确保HTML5的push状态。<p>
```
$locationProvider.html5Mode(true);
```
[loactionProvider](https://docs.angularjs.org/api/ng/provider/$locationProvider)是什么和用来做什么的？他是内置的AngularJS服务为了配置路由信息。使用这个服务后，你可以使用[HTML5 pushState](http://html5demos.com/history)这样的API，或者改变URL前缀从<code>#</code>到<code>!#</code>类似的。如果你打算在你的AngularJS应用中使用Disqus组件，你需要做一些事情。简单的增加一些<code>locationProvider</code>参数给config回调函数，就可以让AngularJS注入这个服务和应用它。<p>
```
angular.module('MyApp', ['ngCookies', 'ngResource', 'ngMessages', 'ngRoute', 'mgcrea.ngStrap'])
  .config(function($locationProvider) {
    $locationProvider.html5Mode(true);


  });
```
当你压缩这个script文件时会发生什么？这个<code>locationProvider</code>的参数会改变成其它模糊的名字，AngularJS会不知道注入哪个服务，你可以通过给这个方法加注释来解决名字依赖的关系。<p>
```
angular.module('MyApp', ['ngCookies', 'ngResource', 'ngMessages', 'ngRoute', 'mgcrea.ngStrap'])
  .config(['$locationProvider', function($locationProvider) {
    $locationProvider.html5Mode(true);


  }]);
```
在这个数组中每一个字符串都是服务的名字，用来注入的参数。从现在开始要使用这个符号，我们将要在接下来压缩和连接script。<p>
接下来，需要路由处理以下页面:<br>
<ul>
<li>Home————展现一系列受欢迎的电视剧</li>
<li>Details————展示电视剧的细节</li>
<li>Login————用来登录</li>
<li>Signup————用来注册</li>
<li>Add————用来加入新的电视页</li>
</ul>
在配置中注入这个[$routerProvider]，加入这些路由。<p>
```
$routeProvider
  .when('/', {
    templateUrl: 'views/home.html',
    controller: 'MainCtrl'
  })
  .when('/shows/:id', {
    templateUrl: 'views/detail.html',
    controller: 'DetailCtrl'
  })
  .when('/login', {
    templateUrl: 'views/login.html',
    controller: 'LoginCtrl'
  })
  .when('/signup', {
    templateUrl: 'views/signup.html',
    controller: 'SignupCtrl'
  })
  .when('/add', {
    templateUrl: 'views/add.html',
    controller: 'AddCtrl'
  })
  .otherwise({
    redirectTo: '/'
  });
```
![Alt text](http://sahatyalkabov.com/images/blog/tvshow-tracker-11.png)
对于每一个路由都有一个控制器和模板，如果你有一个页面大部分是静态内容，你甚至不需要设置一个控制器。如果你立即重载这个页面，并且打开开发者工具，你会看到404错误，因为我们还没有任何模板。<p>
在public/views文件夹中创建<code>home.html</code>，用来存放所有的AngularJS模板。
```
<div class="jumbotron">
  <div class="container">
    <ul class="alphabet">
      <li ng-repeat="char in alphabet">
        <span ng-click="filterByAlphabet(char)">{{char}}</span>
      </li>
    </ul>
    <ul class="genres">
      <li ng-repeat="genre in genres">
        <span ng-click="filterByGenre(genre)">{{genre}}</span>
      </li>
    </ul>
  </div>
</div>

<div class="container">
  <div class="panel panel-default">
    <div class="panel-heading">
      {{headingTitle}}
      <div class="pull-right">
        <input class="search" type="text" ng-model="query.name" placeholder="Search...">
      </div>
    </div>
    <div class="panel-body">
      <div class="row show-list">
        <div class="col-xs-4 col-md-3" ng-repeat="show in shows | filter:query | orderBy:'rating':true">
          <a href="/shows/{{show._id}}">
            <img class="img-rounded" ng-src="{{show.poster}}" width="100%"/>
          </a>
          <div class="text-center">
            <a href="/shows/{{show._id}}">{{show.name}}</a>
            <p class="text-muted">Episodes: {{show.episodes.length}}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
```
如果你使用bootstrap CSS框架，所有的css你都会很熟悉。这里也有一些指令在这里，这个<code>ng-repeat</code>指令将会在这个页面的控制器中从一个数组中迭代指定条目。<p>
让我们看看这个代码段：
```
<li ng-repeat="char in alphabet">
  <span ng-click="filterByAlphabet(char)">{{char}}</span>
</li>
```
它在一个叫<code>MainCtrl</code>中，预定义一个叫<code>alphabet</code>的数组。这个<code>refers</code>指的是这个数组中独立的条目，在这个数组中的字母。当你点击这个字母就会启动在<code>specified</code>指定的<code>filterByAlphabet</code>方法。这里我们把指定的字母传入<code>filterByAlphabet(char)</code>，否则不知道过滤哪个字母。<p>
另一个<code>ng-repeat</code>是展现电视剧的缩略图和名字。<p>
```
<div class="col-xs-4 col-md-3" ng-repeat="show in shows | filter:query | orderBy:'rating':true">
  <a href="/shows/{{show._id}}">
    <img class="img-rounded" ng-src="{{show.poster}}" width="100%"/>
  </a>
  <div class="text-center">
    <a href="/shows/{{show._id}}">{{show.name}}</a>
    <p class="text-muted">Episodes: {{show.episodes.length}}</p>
  </div>
</div>
```
在AngularJS中你可以过滤和排序结果，在以上的代码中，缩略图是靠等级和在input中输入过滤的。<p>
```
<input class="search" type="text" ng-model="query.name" placeholder="Search...">
```
这个判断原因是<code>query.name</code>而不是query，因为我们只想通过TVshow的名字来进行过滤，而不是靠总结、排名、开始播出的时间等进行过滤。<p>
接下来要创建一个新的文件<code>main.js</code>在public/controllers文件夹，然后把它加入到<code>index.html</code>中。<p>
```
<script src="controllers/main.js"></script>
```
```
angular.module('MyApp')
  .controller('MainCtrl', ['$scope', 'Show', function($scope, Show) {

    $scope.alphabet = ['0-9', 'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J',
      'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X',
      'Y', 'Z'];

    $scope.genres = ['Action', 'Adventure', 'Animation', 'Children', 'Comedy',
      'Crime', 'Documentary', 'Drama', 'Family', 'Fantasy', 'Food',
      'Home and Garden', 'Horror', 'Mini-Series', 'Mystery', 'News', 'Reality',
      'Romance', 'Sci-Fi', 'Sport', 'Suspense', 'Talk Show', 'Thriller',
      'Travel'];

    $scope.headingTitle = 'Top 12 Shows';

    $scope.shows = Show.query();

    $scope.filterByGenre = function(genre) {
      $scope.shows = Show.query({ genre: genre });
      $scope.headingTitle = genre;
    };

    $scope.filterByAlphabet = function(char) {
      $scope.shows = Show.query({ alphabet: char });
      $scope.headingTitle = char;
    };
  }]);
```
这里有两个我在使用<code>ng-repeat</code>指令时，前面提到过的数组，<code>alphabet</code>和<code>genre</code>，这个<code>show</code>服务中提到过的两个数组。这个<code>show</code>会自动通过AngularJS注入。我们还没有创建这个服务，所以当加载这个页面的时候，会发生如下错误。Unknown provider: ShowProvider <- Show.<p>
我们在public/services文件夹直接创建一个<code>show.js</code>，不要忘记把它加入到<code>index.html</code>中。<p>
```
<script src="services/show.js"></script>
```
```
angular.module('MyApp')
  .factory('Show', ['$resource', function($resource) {
    return $resource('/api/shows/:_id');
  }]);
```
这个你见过的最简单的服务，要多亏了<code>angular-resource.js</code>这个模块，为我们做了繁重的工作。这个$resources是一个完美的[RESTFUL](https://docs.angularjs.org/api/ngResource/service/$resource)支持模块。现在我们需要的是查询整个节目和介绍通过id来实现。刷新这个页面你将会看到api/shows 404 (Not Found)错误，其他的东西将会像其它期望的那样发生。<p>
![Alt text](http://sahatyalkabov.com/images/blog/tvshow-tracker-12.png)
让我们回到Express应用，实施数据库的schemas结构和API router。<p>
####Step 4: Database Schemas
2014年6月8日更新。<p>
安装[mongoose](http://mongoosejs.com/)和通过[bcryptjs](https://github.com/dcodeIO/bcrypt.js)下面的命令。<p>
```
npm install --save mongoose bcryptjs
```
在<code>server.js</code>中加入下面两行<p>
```
var mongoose = require('mongoose');
var bcrypt = require('bcryptjs');
```
就在下面加入mogobd的数据结构<p>
```
var showSchema = new mongoose.Schema({
  _id: Number,
  name: String,
  airsDayOfWeek: String,
  airsTime: String,
  firstAired: Date,
  genre: [String],
  network: String,
  overview: String,
  rating: Number,
  ratingCount: Number,
  status: String,
  poster: String,
  subscribers: [{
    type: mongoose.Schema.Types.ObjectId, ref: 'User'
  }],
  episodes: [{
      season: Number,
      episodeNumber: Number,
      episodeName: String,
      firstAired: Date,
      overview: String
  }]
});
```
schema数据结构是你的数据在MongoDB中的数据表示，这里是一个可以存储特殊类型的存档。这块存档是独特的、需要的，只能包含了特定字符串。<p>
上面所有的内容和[TheTVDB.com API](http://thetvdb.com/)的数据回应几乎是完全匹配。下面有两点需要记一下：<p>
1.这个默认的字段<code>_id</code>已经被TVDB里的ID数值重写，没有在这个字段里有两个<code>_id</code>和<code>showId</code>。<p>
2.这个<code>subscribers</code>字段是用户的ID，我们还没有创建schema结构，基本上是用户文档的引用。<p>

接下来我们写一个用户的schema结构
```
var userSchema = new mongoose.Schema({
  email: { type: String, unique: true },
  password: String
});

userSchema.pre('save', function(next) {
  var user = this;
  if (!user.isModified('password')) return next();
  bcrypt.genSalt(10, function(err, salt) {
    if (err) return next(err);
    bcrypt.hash(user.password, salt, function(err, hash) {
      if (err) return next(err);
      user.password = hash;
      next();
    });
  });
});

userSchema.methods.comparePassword = function(candidatePassword, cb) {
  bcrypt.compare(candidatePassword, this.password, function(err, isMatch) {
    if (err) return cb(err);
    cb(null, isMatch);
  });
};
```
这里我们使用[pre-save mongoose middleware ](http://mongoosejs.com/docs/middleware.html)和[ instance method](http://mongoosejs.com/docs/guide.html#methods)用于密码验证，这些代码直接从[passport-local](https://github.com/jaredhanson/passport-local)获取。<p>
现在我们这里已经有schemas结构了，已经创建了一个mongoose模型用于mongodb数据库的查询。schema结构仅仅是数据的抽象表示，另一方面说这个模型是MongoDB数据库增删减查的粘结剂。<p>
```
var User = mongoose.model('User', userSchema);
var Show = mongoose.model('Show', showSchema);
```
链接数据库的代码。<p>
```
mongoose.connect('localhost');
```
通过<code>mongod</code>启动MongoDB服务器，然后重新运行<code>server.js</code>让我们的应用继续运行。<p>
####第四步：Express API Routes
我们现在要创建两个路由，第一个是用于所有电视剧的查询，另一个是针对查询单个ID的电视剧。<p>
在中间件后面增加这些路由：
```
app.get('/api/shows', function(req, res, next) {
  var query = Show.find();
  if (req.query.genre) {
    query.where({ genre: req.query.genre });
  } else if (req.query.alphabet) {
    query.where({ name: new RegExp('^' + '[' + req.query.alphabet + ']', 'i') });
  } else {
    query.limit(12);
  }
  query.exec(function(err, shows) {
    if (err) return next(err);
    res.send(shows);
  });
});
```







