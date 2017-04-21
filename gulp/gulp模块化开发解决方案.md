项目结构：
```
project
|---app
|---bower_components
|---node_modules
|---package.json
|---bower.json
|---
```
```
var gulp  = require('gulp');
var $ = require('gulp-load-plugins')();
var open = require('open'); //打开指定默认浏览器
var rename  = require('gulp-rename');
var sftp = require('gulp-sftp');

// gulp.plumber 编译出错时，不中断编译
var app = {
	srcPath: 'app/src/',
	devPath: 'app/build/',
	prdPath: 'app/wx/'
};

gulp.task('lib',function(){
	gulp.src(['bower_components/**/*.js','lib/**/*.js'])
		.pipe($.plumber())
		.pipe(gulp.dest(app.srcPath+'vendor/'))
		.pipe(gulp.dest(app.devPath+'vendor/'))
		.pipe(gulp.dest(app.prdPath+'vendor/'))
		.pipe($.connect.reload())
})

gulp.task('html', function(){
	gulp.src(app.srcPath+ '**/*.html')
		.pipe(rename(function(path){
					if(path.basename=='index'){
						path.dirname = './';
					}else{
						path.dirname = './view';
					}
				}))
		.pipe($.plumber())
		.pipe(gulp.dest(app.devPath))
		.pipe(gulp.dest(app.prdPath))
		.pipe($.connect.reload())
})

gulp.task('json', function(){
	gulp.src(app.srcPath+ 'data/**/*.json')
		.pipe($.plumber())
		.pipe(gulp.dest(app.devPath+ 'data/'))
		.pipe(gulp.dest(app.prdPath+ 'data/'))
		.pipe($.connect.reload())
})

gulp.task('concatless', function(){
	gulp.src(app.srcPath+'components/**/*.less')
		.pipe($.concat('bundle.less'))
		.pipe(gulp.dest(app.srcPath+'style/'))
})
gulp.task('less',['concatless'], function(){
	gulp.src(app.srcPath + 'style/index.less')
		.pipe($.less())
		.pipe(gulp.dest(app.devPath + 'css/'))
		.pipe($.cssmin())
		.pipe(gulp.dest(app.prdPath + 'css/'))
		.pipe($.connect.reload())
})

gulp.task('js', function(){
	gulp.src(app.srcPath + 'components/**/*.js')
		.pipe($.plumber())
		.pipe($.concat('index.js'))
		.pipe(gulp.dest(app.devPath + 'js/'))
		// .pipe($.uglify())
		.pipe(gulp.dest(app.prdPath + 'js/'))
		.pipe($.connect.reload())
})

gulp.task('image', function(){
	gulp.src(app.srcPath + 'image/**/*')
		.pipe(gulp.dest(app.devPath + 'image/'))
		.pipe($.imagemin())
		.pipe(gulp.dest(app.prdPath + 'image/'))
		.pipe($.connect.reload())
})

gulp.task('clean', function(){
	gulp.src([app.devPath, app.prdPath])
		.pipe($.clean())
})

gulp.task('sftphtml',['html'], function(){
	gulp.src(app.prdPath+'/index.html')
		.pipe(sftp({
			host: '120.27.49.79',
			user: 'licai',
			pass: 'Maizi2017',
			remotePath: '/home/licai/wx'
		}))
	gulp.src(app.prdPath+'/view/*.html')
		.pipe(sftp({
			host: '120.27.49.79',
			user: 'licai',
			pass: 'Maizi2017',
			remotePath: '/home/licai/wx/view'
		}))
})
gulp.task('sftpcss', ['less'], function(){
	gulp.src(app.prdPath+'/css/*.css')
		.pipe(sftp({
			host: '120.27.49.79',
			user: 'licai',
			pass: 'Maizi2017',
			remotePath: '/home/licai/wx/css'
		}))
})
	
gulp.task('sftpjs', ['js'], function(){
	gulp.src(app.prdPath+'/js/*.js')
		.pipe(sftp({
			host: '120.27.49.79',
			user: 'licai',
			pass: 'Maizi2017',
			remotePath: '/home/licai/wx/js'
		}))
})
gulp.task('sftp', ['sftphtml', 'sftpcss', 'sftpjs']);

gulp.task('build',['js','less','lib','html','json']);
gulp.task('serve', ['build'], function(){
	$.connect.server({
		root: [app.devPath],
		livereload: true,
		port: '7890'
	})
	// open('http://localhost:1234');

	gulp.watch('bower_components/**/*.js', ['lib'])
	gulp.watch(app.srcPath + 'components/**/*.less', ['sftpcss'])
	gulp.watch(app.srcPath + '**/*.html',['sftphtml'])
	gulp.watch(app.srcPath + 'data/**/*.json', ['json'])
	gulp.watch(app.srcPath + 'components/**/*.js', ['sftpjs'])
	
	// gulp.watch(app.prdPath+'/view/*.html', ['sftphtml'])
	// gulp.watch(app.prdPath+'/css/*.css', ['sftpcss'])
	// gulp.watch(app.prdPath+'/js/*.js', ['sftpjs'])
})

gulp.task('default', ['serve'])











```