需要借助gulp-rename插件，<a href="https://www.npmjs.com/package/gulp-rename">gulp-rename文档</a>， 清空文件的`dirname`即可

单个文件
-
```
var gulp = require('gulp');
var rename = require('gulp-rename');

gulp.task('default', function(){
	gulp.src('./src/**/*.html')
		.pipe(rename({
			dirname: '',
			extname: '.html'
		}))
		.pipe(gulp.dest('./dist/views'))
})
```

多个文件不同处理
-
```
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
```