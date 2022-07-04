ps:这.md文件写的可能有点拉胯（自信点把可能去掉哈哈哈，不知道为什么格式还全乱了呜呜呜呜）
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
# write代码详解
## index.js
    index.js为主文件，首先做的第一件事就是在window变量上挂载my_TemplateEngine属性，使得只要用src进行导入，该HTML页面上的window上就有了该属性，该属性只有一个方法render，作用是传入一个倒引号所包围的HTML代码和data，返回的是将数据整理到HTML中的代码
    
    mustache的原理是把相关的模板引擎转化为token，在将数据放置于token中，最后把整理好的代码返回，现在我们根据这个逻辑，模拟mustache的这个操作以至于达到手写模板引擎的目的
## scanner.js
    扫描器类，当有模板引擎经常传入时，我们要做的第一件是是把他转换成token，但是转化成token之前，我们需要检测一下，哪里使用了模板引擎，这个时候就需要使用scanner类了，这个类中有两个方法，一个是scan，一个是scanUtil。
    
    scan的功能弱，就是走过指定的内容，没有返回值；而scanUtil让指针进行扫描，直到遇见指定内容结束，并且能够返回结束之前路过的文字
    
    他们两个呢结合起来就是找到需要被替换的关键了，查找的原理不就是先查找{{然后记录下内容放到token，再跳过{{，再找}}记录token后跳过}}，通过这样的操作来完成检索
## parseTemplateToTokens
    作用：将模板字符串转化为token（刚刚使用scanner是解析{{}}），原理还是在之前scanner中描述过（先查找{{然后记录下内容放到token，再跳过{{，再找}}记录token后跳过}}）
    
    哦对，在此之上添加了要是文字内容呢，就要使用“text”，要是模板字符串要使用“name”这样方便后续的数据插入操作
    
    整理完成之后呢，tokens也就获得了，return即可
## nestTokens

    函数的功能是折叠tokens，将#与/之间的数据折叠起来，parseTemplateToTokens再将字符串转化为token的时候需要使用到这个辅助函数
    
    试想，要是需要#开头的怎么办呢，他们需要在同一个数组里面进行继续扩展，这个函数就是实现这个操作的
    
    数据的实现流程使用到数据结构的栈，遇到#进栈需要/出栈，把数据结构体现的淋漓尽致，不得不说写着东西的人真的是牛逼
## renderTemplate
    函数的功能是让token数组编程DOM字符串，也是这个函数最终要完成的目的
    
    实现的原理很简单，首先对token进行循环遍历操作，看到text打头的就直接进行拼接即可，看到“name”开头的字符串的转化，在拼接即可
    
    最后把所得到的str进行return操作，即完成操作。
    
    lookup，parseArray是完成这个功能的两个功能函数
# lookup
    当我们使用的时候，会经常的使用“.”，js中可以可以使用a.b.c，但是不能使用m[b.c]获得相关的数据
    
    而这样的操作我们又是无法控制的，因此需要手写一个lookup函数，以便使得其在转化数据的时候，能转化成真正data中的数据而不是undefined
# parseArray
    在renderTemplate中，遇到二维数据的话，那肯定是要进行迭代操作
    
    那这个功能函数的作用为处理数组，结合renderTemplate实现递归
    
    实际上处理的时候还是调用了renderTemplate，但是这样写的话，层次分明，更容易理解其中的结构
    tip：
        用户会使用{{.}}来表示占位（具体情况可在基础使用篇中查看），那这样的话就不能获得相关的数据了
    
    但是用户使用.那一定是需要进行二维数组的循环操作，因此在此函数中怂恿了一个非常巧妙的方法
        result+=renderTemplate(token[2],{
         ...v[i],
         ".":v[i]
     });
    
    普通的就直接展示，同时给.语法给赋值，这样用户在使用.的时候也能进行展示
# end
    已经把所有的内容都展示完毕了
    
    不得不说，这是站在巨人的肩膀上前行，看了一下源代码，不得不说当时设计的时候经历了多少代码的改进
    
    只是完成了一点简答的操作，还有更多的源码解析，等之后有着更好的基础有那能力的时候再去完成
    
    end

