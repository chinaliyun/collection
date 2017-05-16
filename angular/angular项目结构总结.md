这样的一个项目开发结构考虑了太多的因素：
1. 不想在开发调试某一个页面的时候，还要到controller、directives、filters中找对应的文件，因此产生了按照页面把他所包含的js(*.controller.js, *.directive.js, *.filter.js, *.service.js等)、less、html全部放在一起的想法
2. 在Mac开发中，忽然有一天编译的特别慢，电脑也卡了，经过排除，发现是图片的问题，由于在开发中的图片都是没有经过优化的，一些图片甚至超过了2M大小，因此就想把图片都放在一个文件夹中，这个文件夹要满足两方面的条件：
	- 在html或者less中引入的时候，固定格式为"/image/test/"
	- 文件夹不会被gulp.clean任务清除
3. 有一个项目中出现了多个页面都要获取一个相同类型的数据、比如用户的信息，所以在components中专门拿出来一个modules文件夹，来放置service文件和测试用的json文件

开发目录
-
```js
/   //根目录
|---app
|---components
|	|---common
|	|	|---appHead
|	|	|	|---appHead.directive.js
|	|	|	|---appHead.less
|	|	|	|---appHead.html
|	|	|---其他通用组件
|	|
|	|---global	//全局配置文件，包括http的重写、router、cache、html过滤器等
|	|	|---cache
|	|	|	|---cache.factory.json 	// 处理cookie相关的事件
|	|	|---router
|	|	|	|---router.config.js 	// 处理路由规则
|	|	|	|---state.config.js 	// 处理路由发生变化时的事件
|	|	|---http
|	|	|	|---http.factory.js 	// 重新包装http方法
|	|	|---html	
|	|	|	|---html.filter.js 		// 把$sce.trustAsHTML包装为一个过滤器
|	|
|	|---module	//保存所有数据模型的调用方法，这里要注意，如果有好几个页面都要获取同一个类型的数据的时候，才需要这个文件夹，否则这些service和json文件是放在对应的文件夹内
|	|	|---**.service.js 			// 某个获取数据的服务
|	|	|---**.json 	 			// 测试数据
|	|
|	|---其他页面组件
|	|
|	|---app.js 						// 入口文件
|	|
|	|---index.html 					// 首页入口
|	
|---output 	//输出文件
|	|---js
|	|	|---index.js
|	|
|	|---css
|	|	|---index.js
|	|
|	|---data	测试数据打包出来的文件
|	|	|---*.json
|	|
|	|---views	打包出来的各种模板文件
|	|	|---appHead.html
|	|	|---appFoot.html
|	|	|---appTabs.html
|	|
|	|---image
|	|	|---test 	//测试用到的图片文件
|	|	|---其他图片文件
|	|
|	|---vendor // 有一些插件没有less文件只能在html中引入的时候，需要编译到这个文件夹内
|	
|---style
|	|---index.less		// 首页样式文件，会定义一些类似style reset的样式文件，这个里面会引入下面的三个less文件，如果vendor中有要引入的文件，也要引进来
|	|---bundle.less		// 通过components里面less打包出来的文件	
|	|---common.less 	//  全局通用的样式文件
|	|---variable.less 	//  less变量文件
|	|---vendor 			//  全局级的样式文件，比如bootstrap的less文件可以编译到这里
|	
|---bower_components	// 需要前端引入的包通过bower安装，这样gulp任务运行的时候，只需要编译bower_components文件夹中的js、less、font文件到指定目录即可
|	
|---node_modules		// 项目开发时候需要用到的辅助开发包，主要包含gulp相关的包
|	
|---bower_json
|	
|---package.json
|	
|---gulpfile.js
```
gulpfile.js
-
```
var gulp = require('gulp');
var $ = require('gulp-load-plugins')();
var open = require('open'); //打开指定默认浏览器
var concat = require('gulp-concat');
var rename = require('gulp-rename');
// gulp.plumber 编译出错时，不中断编译

var appPath = './app/';
var opt = './app/output/';

gulp.task('lib', function() {
    gulp.src(['bower_components/**/*.js', 'lib/**/*.js'])
        .pipe($.plumber())
        .pipe(gulp.dest(opt + '/vendor/'))
        .pipe($.connect.reload())
})

gulp.task('html', function() {
    gulp.src([appPath + '/components/**/*.html', appPath + '/index.html'])
        .pipe(rename(function(path) {
            if (path.basename == 'index') {
                path.dirname = './';
            } else {
                path.dirname = './view';
            }
        }))
        .pipe($.plumber())
        .pipe(gulp.dest(opt + '/'))
        .pipe($.connect.reload())
})

gulp.task('json', function() {
    gulp.src(appPath + 'components/**/*.json')
        .pipe(rename({
            dirname: ''
        }))
        .pipe($.plumber())
        .pipe(gulp.dest(opt + '/data/'))
        .pipe($.connect.reload())
})

gulp.task('concatless', function() {
    return gulp.src(appPath + 'components/**/*.less')
        .pipe($.concat('bundle.less'))
        .pipe(gulp.dest(appPath + 'style/'))
})
gulp.task('less', ['concatless'], function() {
    gulp.src(appPath + 'style/index.less')
        .pipe($.less())
        // .pipe($.cssmin())
        .pipe(gulp.dest(opt + '/css/'))
        .pipe($.connect.reload())
})

gulp.task('js', function() {
    gulp.src([appPath + 'app.js', appPath + 'components/**/*.js'])
        .pipe($.plumber())
        .pipe($.concat('index.js'))
        // .pipe($.uglify())
        .pipe(gulp.dest(opt + '/js/'))
        .pipe($.connect.reload())
})



gulp.task('clean', function() {
    gulp.src([opt+'!(image)/'])
        .pipe($.clean())
})

gulp.task('build', ['js', 'less', 'lib', 'html', 'json']);
gulp.task('serve', ['build'], function() {
    $.connect.server({
            root: [opt],
            livereload: true,
            host: 'cao.com',
            port: '7891'
        })
        <!-- 如果需要自动打开浏览器可以去掉下面得到注释，以MAC为例 -->
        // open('http://localhost:1234','Google Chrome');


    gulp.watch('bower_components/**/*.js', ['lib'])
    gulp.watch(appPath + 'components/**/*.less', ['less'])
    gulp.watch(appPath + '/style/!(bundle).less', ['less'])
    gulp.watch(appPath + 'components/**/*.html', ['html'])
    gulp.watch(appPath + 'components/**/*.json', ['json'])
    gulp.watch(appPath + 'components/**/*.js', ['js'])

})

gulp.task('default', ['serve'])

```

bower.json
-
```
{
  "name": "webapp",
  "authors": [
    "liyun <chinaliyun92@gmail.com>"
  ],
  "description": "angularjs webapp",
  "main": "angular",
  "keywords": [
    "angularjs"
  ],
  "license": "MIT",
  "homepage": "",
  "ignore": [
    "**/.*",
    "node_modules",
    "bower_components",
    "test",
    "tests"
  ],
  "dependencies": {
    "angular": "^1.6.2",
    "angular-ui-router": "ui-router#^0.4.2",
    "angular-cookies": "^1.6.3",
    "angular-validation": "^1.4.3",
    "angular-touch": "^1.6.3",
    "angular-animate": "^1.6.3"
  }
}

```

package.json
-
```
{
  "name": "webapp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "directories": {
    "test": "test"
  },
  "dependencies": {
    "gulp": "^3.9.1"
  },
  "devDependencies": {
    "gulp": "^3.9.1",
    "gulp-clean": "^0.3.2",
    "gulp-concat": "^2.6.1",
    "gulp-connect": "^5.0.0",
    "gulp-cssmin": "^0.1.7",
    "gulp-html2js": "^0.4.2",
    "gulp-imagemin": "^3.1.1",
    "gulp-less": "^3.3.0",
    "gulp-load-plugins": "^1.5.0",
    "gulp-plumber": "^1.1.0",
    "gulp-rename": "^1.2.2",
    "gulp-uglify": "^2.0.1",
    "open": "^0.0.5",
    "webpack-dev-server": "^2.4.1"
  },
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}

```