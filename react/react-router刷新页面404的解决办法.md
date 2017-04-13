新项目看了一下react 4x以及react-router 4x版本，通过点击跳转到相应的路由页面没有问题，但跳转后刷新页面就会出现404错误，网上到处都查不到资料，只知道使nginx的rewrite解决，没有看到实际案例，自己写了一个简单的案例，已经测试通过，以供查看，不对之处请联系我。
```
  //以正则的形式设置 "/" 路由下的访问地址，这里之所以把原先的字符串匹配改成正则匹配，
  //是因为下面的rewrite里面需要正则匹配模式，这个地方只是自己的理解，但实测有效
location ~* ^/$ {
     root /usr/local/var/nginxwww/react-resource/11-router/src;
     index  index.html ;
}
//设置了一个访问根目录下index.html文件的实际访问路径，经过测试这个配置是必须的，因为
//根据第一条规则，根目录下自动访问的是index.html文件，如果这个规则没有配置，服务器会报500错误
location ~* ^/index.html$ {
     root /usr/local/var/nginxwww/react-resource/11-router/src;
     index  index.html ;
}
//设置针对Js文件的解析路径，在weback-dev-server环境中开发，html文件中引入Js的路径可能是一个相对路径，
//使用nginx运行会报Js文件的404错误，此时把html中的Js路径设置为一个绝对路径即可，也可以直接设置成"/bundle.js"，然后下面的规则直接匹配到JS文件夹即可
location ~* \.js$ {
     root /usr/local/var/nginxwww/react-resource/11-router/src/;
}
这条规则同样重要，它定义了当你访问类似于`/index/list`路由时候，服务器的实际访问路径，rewrite规则描述访问这种路由的时候实际访问的是"/"路由， break表示，匹配到这个规则后就停止，不再匹配其 他路由规则，当访问"/"路由的时候，就又回到了第一条规则上去
location ~* /[\w]+(/[\w]?)? {
     rewrite /[\w]+ / break;
     root /usr/local/var/nginxwww/react-resource/11-router/src/;
}
```