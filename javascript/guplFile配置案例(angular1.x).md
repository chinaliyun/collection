```js
var gulp  = require('gulp');
var $ = require('gulp-load-plugins')();//不需要生命其他的gulp模块名称，可以直接通过`$.模块名`的形式调用
var open = require('open'); //打开指定默认浏览器

// gulp.plumber 编译出错时，不中断编译
var app = {
	srcPath: 'app/src/',
	devPath: 'app/build/',
	prdPath: 'app/dist/'
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
		.pipe($.plumber())
		.pipe(gulp.dest(app.devPath))
		.pipe(gulp.dest(app.prdPath))
		// .pipe($.html2js('angular.js'))
		.pipe($.connect.reload())
})

gulp.task('json', function(){
	gulp.src(app.srcPath+ 'data/**/*.json')
		.pipe($.plumber())
		.pipe(gulp.dest(app.devPath+ 'data/'))
		.pipe(gulp.dest(app.prdPath+ 'data/'))
		.pipe($.connect.reload())
})

gulp.task('less',function(){
	gulp.src(app.srcPath + 'style/index.less')
		.pipe($.less())
		.pipe(gulp.dest(app.devPath + 'css/'))
		.pipe($.cssmin())
		.pipe(gulp.dest(app.prdPath + 'css/'))
		.pipe($.connect.reload())
})

gulp.task('js', function(){
	gulp.src(app.srcPath + 'script/**/*.js')
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

gulp.task('build',['image','js','less','lib','html','json']);
gulp.task('serve', ['build'], function(){
	$.connect.server({
		root: [app.devPath],
		livereload: true,
		port: '7890'
	})
	// open('http://localhost:1234');

	gulp.watch('bower_components/**/*.js', ['lib'])
	gulp.watch(app.srcPath + 'style/**/*.less', ['less'])
	gulp.watch(app.srcPath + '**/*.html',['html'])
	gulp.watch(app.srcPath + 'data/**/*.json', ['json'])
	gulp.watch(app.srcPath + 'script/**/*.js', ['js'])
	gulp.watch(app.srcPath + 'image/**/*', ['image'])
	
})

gulp.task('default', ['serve'])











```
