# 模块化

模块化主要解决的问题：解决代码分隔，增强代码可维护用，提高代码复用性，作用域隔离，模块之间的依赖管理，

## 无模块开发

所有的引入使用script标签，并且有依赖关系的必须先引入，否则会报错，页面上就会出现很多script
```
    <script src="jquery.js"></script>
　　<script src="jquery_scroller.js"></script>
　　<script src="main.js"></script>
　　<script src="other1.js"></script>
　　<script src="other2.js"></script>
　　<script src="other3.js"></script>
```
各个引入之间的依赖关系不明显，并且容易造成全局污染，难以维护，所以引入模块化开发是必然的。


## 为什么使用模块化

web应用越来越强大及复杂，代码也越来越冗余，甚至有一些功能重复的代码，依旧需要再写一次。为了解决代码命名冲突，提高代码的可复用性及可维护性，ES2015(ES6)在2015年发布的时候，引入了对模块的原生支持。

## 模块化开发。

模块化开发是将JS程序拆分为可按需导入的单独模块的机制，是一种开发思想。模块可以看成是为了完成某一功能的程序或子程序，并且是可以被复用及替换的，系统代码可以是由各个模块组合而成。

# 如何实现模块化

## 立即执行函数

在早些时候，可以使用立即执行函数创建函数作用域解决命名冲突及污染全局变量的问题
```
!function f(){
  //内部的变量都不会污染全局变量
}()
```

## CommonJS

**服务器端 javascript 模块化解决方案，适用于同步模块加载。**
- CommonJS的一个模块就是一个文件，通过执行文件来加载模块。各个模块之间相互独立，通过全局对象global联系起来。
- CommonJS规定，在每个模块内部有一个module变量，是一个对象，用来表示当前模块。内部有exports属性，是对外的接口。加载某个模块，其实就是加载该模块的module.exports
- require命令：加载模块（同步），第一次加载该脚本时，执行整个脚本，然后在内存中生成一个对象。（第一次加载才执行，有缓存）
  ```
  // a.js
  module.exports = {
    a: 1
  }
  // 引入模块
  var b = require('a.js')
  b.a // 使用
  ```
- 在node.js中，模块化也是采用CommonJS,node为每个模块提供了一个exports变量，用来指向module.exports
  ```
  var exports = module.exports
  //也就是说exports和module.exports指向同一块内存地址，不能对exports赋值，否则会改变内存地址指向。
  ```
- CommonJS特点：
  1. 所有代码都运行在块级作用域，不会污染全局变量。
  2. 模块可以多次加载，但是这会在第一次加载时执行，运行结果会被缓存，再一次加载，读取的就是缓存内容，也就是说CommonJS是按值导入的，导出文件改变，并不影响之前的导入，除非清空缓存，重新导入。因为每一个引用出去的模块对象，都是一个独立的对象。
  3. 模块加载顺序，按照其在代码中出现的顺序。
  4. CommonJ是同步加载，用于服务器端(不适用于浏览器)，所有文件都在本地，主线程对其影响不大。
  5. CommonJS支持动态导入，require(${path}/xx.js)
   

## AMD

**浏览器端 javascript 模块化解决方案，适用于异步模块加载。**
- AMD:Asynchronous Module Definition,异步模块定义，其使用JS语言自实现模块化的规范方案，AMD规范本身出于浏览器中，采用异步的方式加载模块，模块加载的过程中，不会影响它后面语句的运行，在使用时，首先要加载模块化规范实现文件 require.js 及 JS 主文件，所有依赖这个模块的语句，都定义在一个回调函数中，等待加载完成之后，回调函数才会被执行。**适合运用在浏览器中异步加载模块，并行加载多个模块，但不能解决按需加载，对于依赖的模块不管是否需要都会加载**
- 主要由require.config()、define()、require 三个函数实现。require.config() 用于声明基本路径和模块名称；define() 用于定义模块对象；require() 则用于加载模块并使用。
- define(id?,dependenics?,factory)：定义一个模块
  1. id：模块名称，或者模块加载器请求的指定脚本的名字；
  2. dependencies：是个定义中模块所依赖模块的数组，默认为 [“require”, “exports”, “module”]，举个例子比较好理解，当我们创建一个名为 “alpha” 的模块，使用了require，exports，和名为 “beta” 的模块，需要如下书写（示例1）；
  3. factory：为模块初始化要执行的函数或对象。如果为函数，它应该只被执行一次。如果是对象，此对象应该为模块的输出值；
- require([module],callback)：加载一个模块
  1. 第一个数组为要加载的模块，第二个参数为回调函数：

## CMD

- Common Module Definition，是Sea.js所推广的一个模块化方案的输出，建议define时值传入factory一个参数
- 与AMD的区别：
  1. 对于依赖的模块，AMD 是提前执行，CMD 是延迟执行。不过 RequireJS 从 2.0 开始，也改成可以延迟执行（根据写法不同，处理方式不同）。CMD 推崇 as lazy as possible.
  2. CMD 推崇依赖就近，延迟执行。AMD 推崇依赖前置，提前执行。
   ```
    // AMD

    definie(['./a','./b'],function(a,b){
      //加载模块完毕可以使用
      a.do()
      b.do()
    })

    // CMD

    define(function(require,exports,module){
      //加载模块
      //可以把require写在函数体的任意地方实现延迟加载
      var a = require('./a')
      a.doSomething()
    })
   ```

## UMD

**UMD 允许在环境中同时使用 AMD 与 CommonJS 规范。**
- UMD:Universal Module Definition，通用模块规范，可以进行判断使用哪一种模块。

## ES Module

- ES Module在编译时就能确定模块之间的依赖关系，以及输入和输出变量。
- Module模块化由export和import组成
- export命令用于规定模块的对外接口,导出
- import命令用于输入其他模块提供的功能，导入
- 但是由于ES6目前无法在浏览器中执行，所以，我们只能通过babel将不被支持的import编译为当前受到广泛支持的 require。 
- ES Module是采用响应式方式导入导出的，即导出文件改变，导入一定变。
- 它主要采用异步导入方式，用于浏览器，需要下载文件。
  ```
  // a.js 导出
  export function a(){}
  // 导入
  import xxx from './a.js'
  ```
- CommonJS 模块是运行时加载，ES6 模块是编译时输出接口。
  1. 运行时加载: CommonJS 模块就是对象；即在输入时是先加载整个模块，生成一个对象，然后再从这个对象上面读取方法，这种加载称为“运行时加载”。
  2. 编译时加载: ES6 模块不是对象，而是通过 export 命令显式指定输出的代码，import时采用静态命令的形式。即在import时可以指定加载某个输出值，而不是加载整个模块，这种加载称为“编译时加载”。

**CommonJS 加载的是一个对象（即module.exports属性），该对象只有在脚本运行完才会生成。而 ES6 模块不是对象，它的对外接口只是一种静态定义，在代码静态解析阶段就会生成。**

## 模块化如何选择

1. 在 NodeJS 开发项目中，Node9 已经支持 ES6 语法，完全可以使用 ES6 模块规范。NodeJS 的诞生，本身就基于 Google 的 v8 引擎，没有理由不考虑发挥 v8 的最大潜能
2. 在浏览器 JS 开发项目中，因为从服务器加载文件需要时间，使用 CommonJS 规范肯定是不合适了。至于是使用原生的 ES 模块规范，还是使用sea.js，要看具体场景。
    - 如果想页面尽快加载，sea.js 适合；
    - 如果是单页面网站，适合使用原生的 ES6 模块规范。
    - 浏览器并非只有 Chrome 一家，对于没有使用 v8 引擎的浏览器，使用 ES6 原生规范的优势就又减少了一点。




