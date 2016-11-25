# 利用grunt编译并发布gitbook

由于gitbook每次build都会重新生成整个目录，所以你需要利用gulp-gh-pages或grunt-gh-pages等工具进行发布。

这样一键gulp deploy可以完成指定目录_book发布gh-pages。

## package.js

```js
"devDependencies": {
    "grunt": "~0.4.5",
    "grunt-gitbook": "~1.5.0",
    "grunt-gh-pages": "~0.10.0",
    "grunt-contrib-clean": "~0.6.0",
    "grunt-http-server": "~1.4.0"
},
"peerDependencies": {
    "grunt": "~0.4.5"
}
```

对于旧有项目，为了更新插件版本，可以先删除原来已经安装的插件后重新安装：

```bash
rm -rf node_modules/*
npm install
```

## 参考项目

* [GitbookIO/grunt-gitbook](https://github.com/GitbookIO/grunt-gitbook)

    Generate GitBook website from a repository.

* [regularjs/guide](https://github.com/regularjs/guide/tree/master)

    regularjs's offcial guide.

## 文章

* [轻松将gitbook内容发布到github pages上](http://skyao.github.io/2014/04/22/gitbook-publish/)

