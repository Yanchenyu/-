
# babel篇

## babel是如何工作的

babel是一个编译器，传统编译过程？（分词/词法分析，解析/语法分析AST，生成机器指令），babel也类似，三个过程：解析，转换，生成

babel的工作流程，简单来说，就是传入code字符串，经过一系列处理后，吐出code字符串

>Code => do something  => Code

而我们在配置文件中写的```presets```和```plugins```就是在第二阶段 **转换** 过程中使用的

## plugin是干嘛用的

plugin就是用在do something的时候，如果没有plugin，code还是原字符串返回，不作处理

plugin会运行在preset之前

plugin是从前往后，而preset是从后往前执行

## presets是干嘛用的

preset是用来组合plugin的，无需我们手动安装很多plugin，而是提供了一组插件的集合（单点和套餐的区别）

## presets一些常见的配置项的功能及作用

* **env**: 通过配置得知当前目标环境的特点，然后只做必要的转换，如果不写配置项，默认env为latest，也等价于```es2015 + es2016 + es2017```三个相加

## 其它一些工具

* **babel-preset-env**
  * 它是多个preset的集合，并且不断在更新，我们可以通过配置项来决定需要支持的平台（浏览器或者node平台，不同版本下），然后按需加载插件，如果不配置，默认情况下和**babel-preset-latest**等同，会加载从es2015开始的所有preset

* **babel-register**
  * 每当使用```require```命令加载```.js, .jsx, .es, .es6```的后缀名文件时，会先用babel进行转码。 
  * 使用时必须先加载```require('babel-register')```

* **babel-polyfill**
  * babel默认只转换js语法，不转换新的API，比如```Iterator, Generator, Set, Maps, Proxy, Reflect, Symbol, Promise```等全局对象，以及一些定义在全局对象上的方法，比如```Array.from, Object.assign```，如果想用这些方法，就必须使用**babel-polyfill**，它内部集成了```core-js, regenerator```
  * 使用时必须在所有代码运行之前加```require('babel-polyfill')```
  * 缺点：
    * 打出来的包非常大，因为它是一个整体，会一次性将所有的方法都加到原型链上，没用到的那些方法也会加上去，所以可以单独使用```core-js```的某个类库来解决
    * 会污染全局变量，因为直接在原型上做修改
  * 如何解决这些缺点？使用**babel-plugin-transform-runtime和babel-runtime**

* **babel-plugin-transform-runtime和babel-runtime**
  * 简单来说，它能有效的减少重复代码，并且可以实现在需要进行转换的时候才转换，不会像**babel-polyfill**那样一次性全部处理
  * 使用时必须将**babel-runtime**添加到```dependencies```中
  * 那这样的话，谁还用**babel-polyfill**呢，是否有场景是必须要用它，而**babel-runtime**解决不了的呢？有，**<font color='red'>实例方法无法转码</font>**，如
  ```
    '!!!'.repeat(3);
    'hello'.includes('h');
  ```

更新：corejs: 3里提供了对实例方法的转码，所以不用使用babel-polyfill