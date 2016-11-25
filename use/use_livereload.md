# 在gitbook中实现livereload的支持

## 前言

gitbook之前记得是不支持livereload的，所以每次内容更新之后，都要重新启动一次服务器才能在浏览器中看到更新后的内容，这个稍显不便。

好在后来gitbook终于提供了livereload的支持。

## 直接gitbook命令

直接通过下面的命令启动服务器就好了：

> gitbook serve

以下是日志输出：

```shell
sky.ao@SKY-AO /d/work/code/vip/sky.ao/leaning-mercury (master)
$ gitbook serve
Live reload server started on port: 35729
Press CTRL+C to quit ...

info: loading book configuration....
warn: you should specify a gitbook version to use in your book.json, for example
: 2.x.x
info: OK
info: load plugin gitbook-plugin-livereload ....OK
info: >> 1 plugins loaded
info: start generation with website generator
info: clean website generator
info: OK
info: generation is finished

Starting server ...
Serving book on http://localhost:4000
```

可以看到默认已经启动了gitbook-plugin-livereload 这个插件，”Live reload server started on port: 35729”这个表明Live reload server 启动于 默认的35729端口。

gitbook本身的这个功能已经很好了，一般情况直接用就OK。

## 通过grunt

但是，因为一直没有找到一键式将就可以将生成的html文件发布到github pages的方式，所以上面的方案并不完美。

之前写过一个blog，介绍如何使用grunt来显示上述的发布功能，但是测试后发现，这个方式是不能直接支持live reload的。所以研究了一下，发现 grunt-contrib-watch 这个插件可以提供类似的功能，顺便发现grunt-open可以直接在命令行中打开浏览器访问特定URL，这样可以更加的方便。

## 具体做法

package.json文件中，需要增加以下内容：

```grunt
"devDependencies": {
    ......
    "grunt-contrib-watch": "~0.6.1",
    "grunt-open": "~0.2.3"
},
```

Gruntfile.js中增加以下内容：

```grunt
module.exports = function (grunt) {
    ......
    grunt.loadNpmTasks('grunt-contrib-watch');
    grunt.loadNpmTasks('grunt-open');

grunt.initConfig({
    'http-server': {
                'dev': {
                    ......
                    logFn: function(req, res, error) { },

                    //wait or not for the process to finish
                    runInBackground: true
                }
            }, 

    // grunt-open will open your browser at the project's URL
    open: {
        all: {
            path: 'http://127.0.0.1:4002/index.html'
        }
    },

    watch: {
        css: {
            files: ['**/*.md'],
            tasks: ['gitbook'],
            options: {
                spawn: false, 
                livereload: true,
            },
      },
    },
});

grunt.event.on('watch', function(action, filepath, target) {
    grunt.log.writeln(target + ': ' + filepath + ' has ' + action);
});
```

http-server 这里有两个改动：
runInBackground 设置为true，这样才不会阻塞住进程，否则命令跑到这里就不能继续跑后面的watch命令了
增加了 logFn: function(req, res, error) { } ，这样避免太多的日志打印
open和watch的设置是新增加的。 更新test命令，以增加这两个grunt命令：

```grunt
grunt.registerTask('test', [
    'gitbook',
    'http-server',
    'open',
    'watch',
]);
```

## 工作方式

修改之后的test命令，实际是执行以下命令：

1. gitbook 命令生成gitbook的html内容
1. http-server 命令启动http 服务器，记得上面的runInBackground: true 就是为了能继续执行下面的命令
1. open 命令打开浏览器访问URL，我们在open的配置中设置为首页了
1. watch 命令开启livereload 功能，为了能够清晰的看到reload的信息，我们增加了grunt.event.on(‘watch’）的功能，每次打印出来具体reload的内容

watch命令会监控md文件，如果有改动，则自动执行 gitbook 命令重新生成html内容。
这个方案用起来比较爽，一个 grunt test 命令下去，所有工作自动搞定，连浏览器都被自动打开访问到首页，可谓懒人必备。

## 缺陷

但是有个很要命的缺陷：就是内容修改之后watch监听到重新更新html内容之后，还在需要在浏览器中用F5 手工刷新一下。

虽然也不是很大的问题，但是总归不够完美，尤其gitbook serve 命令下已经可以实现浏览器自动刷新更新内容。

找了一下，grunt-contrib-watch 这个插件是提供了对应功能的，只需要在生成的html里面加入一句：

    <script src="//localhost:35729/livereload.js"></script>

但是很遗憾gitbook默认没有地方能让我们加入这一句话，如果非做不可那就只能借助于theme功能，使用自己的样式，然后修改模板，未免有些因小失大了。

更重要的是，怎么用theme，我还不会……

## 文章来自

[sky's blog](http://skyao.github.io/2015/06/15/gitbook-livereload/)
