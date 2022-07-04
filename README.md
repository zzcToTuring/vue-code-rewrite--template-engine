# vue源码解析
这个文件依赖主要是重写了vue的模板引擎
# 什么是vue的模板引擎？
{{}}，v-if，vue-for这样的操作是vue的基础操作吧，这样的操作比较熟悉吧，这操作的底层就是应用到了模板引擎
# 什么是mustache？
他是模板引擎的鼻祖，在vue很早之前就已经诞生了，有些用法和vue相似（是vue抄他的不是他抄vue的...），但是在vue在他之上做了一些改进
比如两者的循环的语法（可能底层的实现原理也不同，可能我没有学到这么深入）不同
在学习vue的底层实现之前，我们不妨来学习一下mustache的底层实现原理
从简到难，一步一步的深入了解框架底层的实现
# jslib文件夹和mustache-study？
在学习mustache模板引擎的底层原理，我们首先要学习如何使用mustache模板引擎的用法
jslib文件夹中存放在mustache的源文件，mustache-study是应用mustache做的一些应用操作
# write文件夹
模块化编程，手写mustache的过程全部放置于此文件夹
想要体会原汁原味的开发过程的话，可以先使用npm i安装相关的包，使用webpack-dev-server（或npm run dev）进行项目的启动。在浏览器端口出入8080即可访问（可通过webpack.config.js）文件件进行重新配置
PS：要是对webpack不了解的话，可以参考webpack-study文件夹，上面记录着自己学习webpack的笔记and代码
# dist文件夹
里面是通过webpack整合好的文件，可直接在HTML页面中使用script标签引入，和mustache的使用方式类似，但是没有他的功能这么强大罢了。只能做到循环迭代和相关的操作，v-if的操作还没有能够实现。还是路漫漫其修远兮呀
与write相比较的话，这个更加简单，但是里面的代码都是使用webpack整合和压缩的，代码的内容不是给人看的，因此想访问代码的情况的时候，应该在write文件夹中，使用为webpack-cli高效而且保存即更新（呜呜呜虽然我已经写了几次死循环把浏览器搞崩溃了）

