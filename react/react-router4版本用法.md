Route组件可能是React Router中需要我们理解和学习的最重要的组件，它最主要的任务是当location发生变化的时候，渲染对应的UI界面，基础用法如下：
```js
import { BrowserRouter as Router, Route } from 'react-router-dom'

<Router>
  <div>
    <Route exact path="/" component={Home}/>
    <Route path="/news" component={NewsFeed}/>
  </div>
</Router>
```
props.exact
-
当这个值设置为true的时候，将以“精准”的模式匹配url：
```
<Route exact path="/one" component={About}/>
```
path	|location.pathname	|exact	|matches?
---|---|---|---
/one	|/one/two	|true|	no
/one	|/one/two	|false	|yes

props.strict
-
当这个值设置为true的时候，将以“严格”模式匹配url, path末尾必须配合一个斜杠，否则不会匹配成功
```
<Route strict path="/one/" component={About}/>
```
path	|location.pathname	|matches?
---|---|---
/one/	|/one	|no
/one/	|/one/	|yes
/one/	|/one/two	|yes
strict和exact模式可以同时使用
-
```
<Route exact strict path="/one" component={About}/>
```
path	|location.pathname	|matches?
---|---|---
/one	|/one	|yes
/one	|/one/	|no
/one	|/one/two	|no