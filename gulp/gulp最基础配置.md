这种配置是最基础的，适合单页面开发

package.json
-
```js
{
  "name": "readhub",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "gulp": "^3.9.1",
    "gulp-concat": "^2.6.1",
    "gulp-connect": "^5.0.0",
    "gulp-cssmin": "^0.2.0",
    "gulp-less": "^3.3.0",
    "gulp-load-plugins": "^1.5.0",
    "gulp-uglify": "^3.0.0",
    "gulp-watch": "^4.3.11"
  }
}


```
gulpFile.js
-
```js
var gulp = require('gulp');
var path = require('path');
var $ = require('gulp-load-plugins')();

var app = path.resolve(__dirname, 'app');
var opt = path.resolve(__dirname, 'app/output/');


gulp.task('html', function(){
	gulp.src(app+'/index.html')
		.pipe(gulp.dest(opt+'/'))
		.pipe($.connect.reload())
})
gulp.task('js', function(){
	gulp.src(app+'/scripts/**/*.js')
		// .pipe($.uglify())
		.pipe(gulp.dest(opt+'/js'))
		.pipe($.connect.reload())
})
gulp.task('lib', function(){
	gulp.src(app+'/lib/**/*.js')
		// .pipe($.concat('index.js'))
		// .pipe($.uglify())
		.pipe(gulp.dest(opt+'/vendor'))
		.pipe($.connect.reload())
})
gulp.task('less', function(){
	gulp.src(app+'/less/**/*.less')
		.pipe($.less())
		.pipe(gulp.dest(opt+'/css'))
		.pipe($.connect.reload())
})

gulp.task('server', function(){
	$.connect.server({
		root: [opt],
		livereload: true,
		port: '7891'
	})
})
gulp.task('default', ['server','build'], function(){
	$.watch(app+'/lib/**/*.js', function(e){
		gulp.start('lib')
	})
	$.watch(app+'/scripts/**/*.js', function(e){
		gulp.start('js')
	})
	$.watch(app+'/less/**/*.less', function(e){
		gulp.start('less')
	})
	$.watch(app+'/index.html', function(e){
		gulp.start('html')
	})
});

gulp.task('build', ['lib','js','less'])


```