---
layout: post
title: 前端自动化构建工具 - Grunt
category: 前端自动化构建工具
tags: 
  - 前端
  - 构建工具
  - Grunt
---

# 前言 - Grunt 到底是啥?

![grunt](http://oflimcy5e.bkt.clouddn.com/grunt%E6%A0%87%E5%BF%97.png)

工作中我们会遇到，对代码(js,css,html)就行加工：压缩、合并、代码检查、测试 等等。

我们需要一个集成的环境 就行操作，Grunt就是这样的工具。

是一个js开发框架，具体功能是以安装插件的形式实现的，所以扩展性无限。

---

# 安装

## 安装 node.js

Grunt是基于Node.js为基础的，所以没装node.js的需要安装下。

> http://nodejs.org/

已经安装的可以更新下

```
npm update -g npm
```

> macos 下加 sudo

## 安装 Grunt CLI

```
npm install -g grunt-cli
```

---

# 配置我们第一个项目

## 建立项目目录

```
/grunt-do
   src/
   css/
   dist/
```

> src 开发的js代码
> css 开发的css代码
> dist 发布代码

## 根目录下创建 `package.json`

```
{
  "name": "myapp",
  "version": "0.1.1",
  "devDependencies": {
  }
}
```

## 安装Grunt

```
npm install grunt --save-dev
```

> package.json `devDependencies` 被加入了依赖配置

```
{
  "name": "myapp",
  "version": "0.1.1",
  "devDependencies": {
    "grunt": "^1.0.1"
  }
}
```

> 多了个 `node_modules` 的目录，有空可以阅读下,暂时我们无视这个目录。

## 根目录下创建 `Gruntfile.js`

```
module.exports = function(grunt) {

    grunt.initConfig({
        pkg: grunt.file.readJSON('package.json')
    });

};
```

> 这是Grunt的配置文件
> initConfig初始pkg属性，这都是套路，读取上面的package.json
>

# 插件

上面我们完成了项目的初始，具体的工作grunt都是以插件的形式实现的。

## 合并代码

- 编写1个js文件

src/mods/module1.js

```javascript
// 模块1
define(function(require, exports, module) {

    console.log("module1 被装载~");

    exports.num = 10;

    exports.add = function(){
        this.num ++;
    };

    exports.get = function(){
        return this.num;
    };

});
```

src/mods/module2.js

```javascript
// 模块2
define(function(require, exports, module) {

    console.log("module2 被装载~");

    exports.num = 10;

    exports.add = function(){
        this.num ++;
    };

    exports.get = function(){
        return this.num;
    };

});
```

- 安装插件

命令行

```
npm install grunt-contrib-concat --save-dev
```

- 编写 Gruntfile.js

```
module.exports = function(grunt) {

    grunt.initConfig({
        pkg: grunt.file.readJSON('package.json'),

        // 合并
        concat:{
            options:{
                separator:'\n',
                banner:'//model合并 \n',
                footer:'\n//--- end ---',
            },
            myapp:{
                src:['src/mods/*.js'],
                dest:'dist/<%= pkg.version %>/module.js'
            }
        },
    });

    grunt.loadNpmTasks('grunt-contrib-concat');

    grunt.registerTask('test', ['concat']);
};
```

> concat.options 配置
> separator 合并文件之间的字符
> banner 头部字符
> footer 底部字符
>
> myapp 是你自己取的名字 可以多开
> src 源文件
> dest 目标文件

- 运行&输出

命令行

```
grunt test
```

查看合并后的文件 dist/<版本号>/module.js

```
//model合并 
// 模块1
define(function(require, exports, module) {

    console.log("module1 被装载~");

    exports.num = 10;

    exports.add = function(){
        this.num ++;
    };

    exports.get = function(){
        return this.num;
    };

});
// 模块2
define(function(require, exports, module) {

    console.log("module2 被装载~");

    exports.num = 10;

    exports.add = function(){
        this.num ++;
    };

    exports.get = function(){
        return this.num;
    };

});
//--- end ---
```

---

## 压缩js代码

- 安装插件

命令行

```
npm install grunt-contrib-uglify --save-dev
```

- 编写 Gruntfile.js

```
module.exports = function(grunt) {

    grunt.initConfig({
        pkg: grunt.file.readJSON('package.json'),

        // 合并
        concat:{
            options:{
                separator:'\n',
                banner:'//model合并 \n',
                footer:'\n//--- end ---',
            },
            myapp:{
                src:['src/mods/*.js'],
                dest:'dist/<%= pkg.version %>/module.js'
            }
        },

        // js压缩
        uglify:{
            module_targer:{
                options:{
                    banner:'/*app:<%= pkg.name %> , module all*/\n',
                    footer:'\n//--- end ---',
                },
                files:{
                    'dist/<%= pkg.version %>/module.min.js':['<%= concat.myapp.dest %>']
                }
            }
        },
    });

    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');

    grunt.registerTask('test', ['concat','uglify']);
};
```

> files 的源文件是上次合并的dest内容，压缩后结果文件 module.min.js
> grunt.registerTask 任务是一个序列 先合并 后压缩 只要有一个环节出错 任务中断

- 运行&输出

命令行

```
grunt test
```

查看 dist/<版本号>/module.min.js

```
/*app:myapp , module all*/
define(function(a,b,c){console.log("module1 被装载~"),b.num=10,b.add=function(){this.num++},b.get=function(){return this.num}}),define(function(a,b,c){console.log("module2 被装载~"),b.num=10,b.add=function(){this.num++},b.get=function(){return this.num}});
//--- end ---
```

> 压缩成功!

---

## 压缩css代码

- 新建css文件

css/index.css

```
.main {
	margin: 10px 0
}


/*多方适用*/

.bread,
.selector,
.type-wrap,
.value {
	overflow: hidden
}

.bread,
.selector,
.details,
.hot-sale {
	margin: 15px 0
}

......

```

- 安装插件

命令行

```
npm install grunt-contrib-cssmin --save-dev
```

- 编写 Gruntfile.js

```
module.exports = function(grunt) {

    grunt.initConfig({
        pkg: grunt.file.readJSON('package.json'),

        // 合并
        concat:{
            options:{
                separator:'\n',
                banner:'//model合并 \n',
                footer:'\n//--- end ---',
            },
            myapp:{
                src:['src/mods/*.js'],
                dest:'dist/<%= pkg.version %>/module.js'
            }
        },

        // js压缩
        uglify:{
            module_targer:{
                options:{
                    banner:'/*app:<%= pkg.name %> , module all*/\n',
                    footer:'\n//--- end ---',
                },
                files:{
                    'dist/<%= pkg.version %>/module.min.js':['<%= concat.myapp.dest %>']
                }
            }
        },

        // css压缩
        cssmin:{
            index:{
                files:{
                    'dist/<%= pkg.version %>/index.min.css':['css/index.css']
                }
            }
        },
    });

    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    grunt.loadNpmTasks('grunt-contrib-cssmin');

    grunt.registerTask('test', ['concat','uglify','cssmin']);
};
```

> 加入cssmin任务

- 运行&输出

命令行

```
grunt test
```

查看 dist/<版本号>/index.min.css

```
.main{margin:10px 0}.bread,.selector,.type-wrap,.value{overflow:hidden}.bread,.details,.hot-sale,.selector{margin:15px 0}.filter,.hot-sale,.selector{border:1px solid #ddd}.key{padding:10px 10px 0 15px}.type-wrap ul li{float:left;list-style-type:none}.sui-btn{border-radius:0}.bread .sui-breadcrumb{padding:3px 15px;margin:0}
```

---

## 代码检查

- 编辑js文件

src/mods/module1.js

```
// 模块1
define(function(require, exports, module) {
>>> haha
    console.log("module1 被装载~");
```

> 第三行加入错误

- 安装插件

命令行

```
npm install grunt-contrib-jshint --save-dev
```

- 编写 Gruntfile.js

```
module.exports = function(grunt) {

    grunt.initConfig({
        pkg: grunt.file.readJSON('package.json'),

        // 检查js
        // http://jshint.com/docs/options/
        jshint:{
            files:['src/**/*.js'],
            // options:{
            //     jQuery:true,
            //     console:true,
            //     module:true,
            //     document:true
            // }
        },
    });

    grunt.loadNpmTasks('grunt-contrib-jshint');

    grunt.registerTask('jshint', ['jshint']);
};
```

- 运行&输出

命令行

```
grunt jshint
```

![grunt-jshint](http://oflimcy5e.bkt.clouddn.com/grunt-jshint.png)

---

## 代码测试

- 编写测试 

test/unit1.html

```
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width">
  <title>QUnit Example</title>
  <link rel="stylesheet" href="../lib/qunit.css">
</head>
<body>
  <div id="qunit"></div>
  <div id="qunit-fixture"></div>
  <script src="../lib/qunit.js"></script>
  
  <script>
  QUnit.test( "hello test", function( assert ) {
    assert.ok( 1 == "1", "Passed!" );
    });
  </script>

</body>
</html>
```

- 安装插件

命令行

```
npm install grunt-contrib-qunit --save-dev
```

- 编写 Gruntfile.js

```
module.exports = function(grunt) {

    grunt.initConfig({
        pkg: grunt.file.readJSON('package.json'),

        // 测试
        qunit: {
            all: ['test/**/*.html']
        },
    });

    grunt.loadNpmTasks('grunt-contrib-qunit');
    grunt.registerTask('qunit', ['qunit']);
};
```

- 运行&输出

命令行

```
grunt qunit
```

---

![grunt-qunit](http://oflimcy5e.bkt.clouddn.com/grunt-qunit.png)

## CMD模式代码发布

- 编写模块依赖代码

src\call.js

```
// 模块call
define(["mods/module1","mods/module2"],function(require, exports, module) {

    var mod1 = require("module1");
    var mod2 = require("module2");

});
```

- 安装插件

命令行

```
npm install grunt-cmd-transport --save-dev
```

- 编写 Gruntfile.js

```
module.exports = function(grunt) {

    grunt.initConfig({
        pkg: grunt.file.readJSON('package.json'),

        // 转换
        transport:{
            options: {
                debug:false,
                include:'all'
            },
            module:{
                files: [{
                    expand: true,
                    cwd: 'src',
                    src: ['**/*.js', '!*.min.js'],
                    dest: 'dist/<%= pkg.version %>',
                    ext: '.js'
                }]
            },
        },
    });


    grunt.loadNpmTasks('grunt-cmd-transport');

    grunt.registerTask('ts', ['transport']);
};
```

- 运行&输出

命令行

```
grunt ts
```

查看 dist/<版本号>/

```
输出到目录
dist/
```

被标准格式化了，加入了cmd模块的id,后期可以方便合并文件.

---

# 配置git `.gitignore`

过滤掉`node_modules`目录

```
node_modules/
```

---

# 拿到一个现有项目

- 安装需要的包文件

```
npm install
```

- 查看 `Gruntfile.js` 的 `grunt.registerTask` 定义

> 执行`grunt`看看是否有错误

```
grunt
```

---

# 需要注意

- 任务的名字不要用插件的缩写，否则会无法运行。

```javascript
// 错误的写法
grunt.registerTask('transport', ['transport']);
```

> 其它名字还有 concat、uglify、cssmin、jshint、qunit、transport ...

- 任务名字 `default` 指默认任务

```javascript
grunt.registerTask('default', ['concat','uglify','cssmin']);
```

运行

```
grunt
```

---

# 资料

> http://gruntjs.com/
> http://www.gruntjs.net/
> http://jshint.com/docs/options/

# 代码

> https://github.com/hans007/JavaScriptCodes/tree/master/gruntjs

[我的博客](https://hans007.github.io)