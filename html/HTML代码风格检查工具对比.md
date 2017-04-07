<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">来自早读君：</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">今天推荐前端开发工具篇，早读君对这个关注不多。对了，想跟大家讨论一个问题，页面html代码在生产环境是否有需要压缩？利弊点，是否有不方便的？</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;"> </span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">好了，正文从这开始～</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;"> </span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">作为一个前端，不可避免同时与三个语言打交道：JS、CSS 和 HTML。而 HTML，超文本标记语言，是其中可编程性最弱的，一直以来得到的关注都较少。加上浏览器对 HTML 逆天的容错支持，即使是错误百出的文档也可以在浏览器里边表现得中规中矩。这样的背景下，绝大部分被产出的 HTML 代码都存在着各种各样的小问题，比如缺少必要的元信息（meta），比如混乱的 class、id 或属性的取值格式；这些问题或影响页面在不同浏览器下的表现，或增大了页面的开发、维护成本。</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">因此，选用一个合适的工具对 HTML 代码进行质量控制会是一件很有意义的事情。本文选择了 Bootlint、AriaLinter、htmllint、HTMLHint 及htmlcs 这五个目前最活跃的相关项目进行对比。除此之外还存在如 tidy、W3C/Mozilla HTML validator 等工具，但它们专注于 HTML 规范，几乎不涉及代码风格上的检查，这里就不做比较。</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">对比角度将主要包括以下几个方面：</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">1）使用及配置</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">2）规则实现及自定义</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">3）性能</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">4）亮点</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="font-size: 16px;"><span style="line-height: 200%; font-family: 宋体;">为了后续说明的便利，这里先对语法风格的规则进行简单的分类，第一类包括</span><span style="line-height: 200%;"> </span><span style="line-height: 200%; font-family: 宋体;">attr-value-double-quotes（使用双引号包围属性值），</span><span style="line-height: 200%;"> </span><span style="line-height: 200%; font-family: 宋体;">max-length（限制单行最大长度），</span><span style="line-height: 200%;"> </span><span style="line-height: 200%; font-family: 宋体;">tag-pair（要求需要显式闭合的标签显式闭合）等；第二类包括</span><span style="line-height: 200%;"> </span><span style="line-height: 200%; font-family: 宋体;">script-in-tail（JavaScript内容要求在页面最后嵌入）,</span><span style="line-height: 200%;"> </span><span style="line-height: 200%; font-family: 宋体;">title-required（要求 title 标签）,</span><span style="line-height: 200%;"> </span><span style="line-height: 200%; font-family: 宋体;">id-class-ad-disabled（不允许在id 或 class 的值中出现 ad_，ad-，_ad，-ad 等）等。这两类规则有很明显的区别，第一类偏重于代码格式（遵循与否都不影响最终语义），这里叫它格式规则；对应地，第二类偏重语义，即最终 document 的表现，这里叫它语义规则。一般情况下，前者更适合在语法分析阶段做，而后者更适合在分析完后基于分析结果（AST / document）进行。</span></span>
</p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="color: rgb(0, 176, 80);"><strong><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">Bootlint</span></strong>
    </span>
</p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">Bootlint 可能是 github 上 star 数最多的 HTML 代码风格检查工具。不过正如其名所暗示的，它由 Bootstrap 团队开发，专注于基于 Bootstrap 的项目。与受关注程度相对应，项目的完善度较高，文档齐全，使用方式包括在浏览器中引入，作为 Grunt 任务、Nodejs 模块及命令行工具。</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">Bootlint 支持规则粒度的配置，但仅限于 disable/ enable。不支持配置文件或行内配置。</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="font-size: 16px;"><span style="line-height: 200%; font-family: 宋体;">Bootlint 专注于基于 Bootstrap 的项目，这一点在它的<span style="color: windowtext;">规则列表</span>中体现得较为明显：Bootlint 提供的大多数规则都明显只适用于 Bootstrap 项目，如 W004（插件 Modal 中不允许使用将被废弃的</span><span style="line-height: 200%;"> </span><span style="line-height: 200%; font-family: 宋体;">remote），W005（如果使用了基于 jQuery 的 Bootstrap插件，则要求页面中引入 jQuery）等。另外，Bootlint实现方式是通过</span><span style="line-height: 200%;"> </span><span style="line-height: 200%; font-family: 宋体; color: windowtext;">Cheerio</span><span style="line-height: 200%;"> </span><span style="line-height: 200%; font-family: 宋体;">对 HTML 代码进行解析，获取到类 jQuery 的</span><span style="line-height: 200%;"> </span><span style="line-height: 200%; font-family: 宋体;">$（选择器）接口，其规则均在</span><span style="line-height: 200%;"> </span><span style="line-height: 200%; font-family: 宋体;">$</span><span style="line-height: 200%;"> </span><span style="line-height: 200%; font-family: 宋体;">基础上实现，这决定了目前的 Bootlint 所能提供的仅限语义规则。而且，Bootlint 不支持添加自定义规则。</span></span>
</p>
<p style="line-height:200%"><span style="font-size: 19px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="font-size: 16px;"><span style="line-height: 200%; font-family: 宋体;">Bootlint 性能一般，Cheerio 内部使用 htmlparser2 进行 HTML 代码的解析，然后将对节点的操作包装为</span><span style="line-height: 200%;"> </span><span style="line-height: 200%; font-family: 宋体;">$</span><span style="line-height: 200%;"> </span><span style="line-height: 200%; font-family: 宋体;">方法，Bootlint 逐规则通过$</span><span style="line-height: 200%;"> </span><span style="line-height: 200%; font-family: 宋体;">查找元素，依据结果进行检查。</span></span>
</p>
<p style="line-height:200%"><span style="font-size: 19px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">针对 Bootstrap 相关的检查可以算是 Bootlint 的亮点之一。Bootlint 的另一个独特之处在于，它支持作为一个服务器运行，提供基于 HTTP 请求的 lint 服务。</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="color: rgb(0, 176, 80);"><strong><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">AriaLinter</span></strong>
    </span>
</p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">AriaLinter 是一个基于规则（Rule based），面向 HTML document 的检查工具。它支持作为一个 Grunt 任务（最推荐的形式）、Nodejs 模块或命令行工具使用。</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">作为一个 Grunt 任务或 Nodejs 模块被调用时，支持传入规则配置。但不支持配置文件或行内配置，及作为单独命令行工具使用时是不可配置的。</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="font-size: 16px;"><span style="line-height: 200%; font-family: 宋体;">AriaLinter 强调了它是“for HTML documents”，即偏语义规则的检查，做法是基于</span><span style="line-height: 200%;"> </span><span style="line-height: 200%; font-family: 宋体; color: windowtext;">jsdom</span><span style="line-height: 200%;"> </span><span style="line-height: 200%; font-family: 宋体;">获取运行时的 document，接着检查其内容、结构。事实上，AriaLinter 几乎没有实现任何格式规则。另外，也不支持自定义规则。</span></span>
</p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">AriaLinter 性能会是个问题，毕竟只是为了得到 document 结构的话，jsdom 太重了。</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">AriaLinter 的亮点之一是贴心地支持了 template 参数，当开启时部分规则不做检查。该特性在模板语法会对 HTML 语法造成破坏时效果有限。</span></p>
<p style="line-height:200%"><span style="font-size: 19px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="color: rgb(0, 176, 80);"><strong><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">htmllint</span></strong>
    </span>
</p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">htmllint 是一个2014年开始的项目，它将自己定位为一个“html5 linter and validator”，它提供了较全面的 rule，实现方式也很特别。有不少的人参与了开发，文档也很齐全，奇怪的是它受到的关注度很低。</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">htmllint 支持作为 Grunt 任务或 Nodejs 模块的使用形式。可传入规则配置。不支持配置文件，支持行内注释配置。</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">htmllint 较好地同时支持了格式规则及语义规则。做法是将两类规则分两步做：</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">1）对代码进行进行逐行 lint</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">2）对代码 parse 完成后，针对 AST 逐节点 lint</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">同时也支持自定义规则。</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">最后在性能上，使用 htmlparser2 解析代码，性能较好；另外逐节点 lint 的方式避免了重复遍历，也有一定性能上的收益。</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="color: rgb(0, 176, 80);"><strong><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">HTMLHint</span></strong>
    </span>
</p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">HTMLHint 是国人出品的 HTML 代码检查工具，定位是“A Static Code Analysis Tool for HTML”。</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">使用方式包括在浏览器中引入，作为 Nodejs 模块，及命令行工具。支持传入配置或指定配置文件。不过与 Bootlint 类似，其规则配置仅限于 disable / enable。</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">与 AriaLinter 相反，HTMLHint 仅在对代码进行 parse 的过程中进行检查。这样带来的好处是性能上的，避免了对 AST / document 的操作；当然这也带来局限性：规则偏格式规则。虽然HTMLHint 也提供一些语义规则，但其实现建立在对 parse 时事件的监听上，显得吃力且不自然。</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">另外，HTMLHint 支持自定义 rule，当然，自定义 rule 也受到上述限制，仅能接触到 parser 而非 AST / doument。</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">HTMLHint 的性能应该是能提供语义规则的工具里最好的。仅有一遍 parse 过程，无 AST / Document 的查找过程。</span></p>
<p style="line-height:200%"><span style="font-size: 19px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="color: rgb(0, 176, 80);"><strong><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">htmlcs</span></strong>
    </span>
</p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">htmlcs 是百度 EFE 出品的的 HTML 代码风格检查工具，项目始于2014年底，应百度内部全面推行代码规范检查的需求而生。</span></p>
<p style="line-height:200%"><span style="font-size: 14px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">htmlcs 支持以 Nodejs 模块，或命令行工具的形式使用，在 grunt / gulp 中使用时需要手动包装。作为模块调用时，可传入自定义配置对象；作为命令行工具使用时，支持配置文件。另外也支持代码行内配置。</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="font-size: 16px;"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">htmlcs 提供了较丰富的规则实现，包括格式规则及语义规则，也支持添加自定义规则。在实现时针对性分为 parse 时规则（类 HTMLHint 实现方式）及面向 document 的规则（类 AriaLinter 的规则行为），前者监听 parser 事件，后者直接读取 document 结构。特别地，这里的 document 结构被进行了包装，依据</span><span style="font-size: 16px; line-height: 200%;"> </span><span style="font-size: 16px; line-height: 200%; font-family: 宋体;"><span style="font-size: 16px; color: windowtext;">DOM 规范</span>实现了大部分的属性及读操作方法。因此自定义的规则可以以操作规范 DOM 的形式操作 document 结构，实现自然、便利。</span>
    </span>
</p>
<p style="line-height:200%"><span style="font-size: 19px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">另外 htmlcs 支持传入额外 linter 对 HTML 中内嵌的JS 及 CSS 内容进行检查。</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">对于可自动修正的规则，htmlcs 提供了 format 方法。自定义规则同样可以自定义对应的 format 行为。页面内嵌的 JS 及 CSS 内容也可以通过传入对应的 format 方法进行格式化。</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">htmlcs 的性能不是优势，处于可接受的程度。HTML 代码的 parse 基于 htmlparser2，但对结果对象有一个封装的过程；另外规则的实现方式（节点查找的频次）也在一定程度上影响最终表现。</span></p>
<p style="line-height:200%"><span style="font-size: 19px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="color: rgb(0, 176, 80);"><strong><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">总结</span></strong>
    </span>
</p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">Bootlint 与 AriaLinter 均短板明显。前者在只针对 Bootstrap 项目时有一定优势；而后者在只关注语义规则时能满足一定需求。</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">htmllint 在大部分情况下能满足基本需求，但优点不明显。</span></p>
<p style="line-height:200%"><span style="font-size: 19px; line-height: 200%; font-family: 宋体;"><br></span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">如果对性能较为敏感，推荐 HTMLHint。</span></p>
<p style="line-height:200%"><span style="font-size: 16px; line-height: 200%; font-family: 宋体;">而 htmlcs 在扩展性、自定义能力上表现突出，覆盖的规则可能也是目前最全的，从个人角度看是大部分情况下的推荐选择。</span></p>
