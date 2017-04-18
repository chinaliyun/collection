需要借助gulp-rename插件，<a href="https://www.npmjs.com/package/gulp-rename">gulp-rename文档</a>， 清空文件的`dirname`即可
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