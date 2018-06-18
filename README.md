# let 和 const 命令

- let命令相似于var命令, 但是let命令所声明的变量, 只在let命令所在的代码块内有效

- ``` js
  var a = [];
  for (var i=0; i<10; i++){
      a[i] = function(){
        console.log(i);
    };
  }
  a[6]();//输出10
  
  var a = [];
  for (let i=0; i<10; i++){
      a[i] = function(){
            console.log(i);
    };
  }
  a[6]();//输出6
  ```

  第一段代码中, 上面代码中，变量i是var命令声明的，在全局范围内都有效，所以全局只有一个变量i。
  每一次循环，变量i的值都会发生改变，而循环内被赋给数组a的函数内部的console.log(i)，里面的i指向的就是全局的i。  
  也就是说，所有数组a的成员里面的i，指向的都是同一个i，导致运行时输出的是最后一轮的i的值，也就是 10    
  
  第二段代码中, 上面代码中，变量i是let声明的，当前的i只在本轮循环有效，所以每一次循环的i其实都是一个新的变量，所以最后输出的是6。  
  你可能会问，如果每一轮循环的变量i都是重新声明的，那它怎么知道上一轮循环的值，从而计算出本轮循环的值？这是因为 JavaScript 引擎内部会记住上一轮循环的值，初始化本轮的变量i时，就在上一轮循环的基础上进行计算

- 另外, for循环特殊:技术设置循环变量的那部分是一个父作用域, 而循环体内部是一个单独的子作用域
  ``` js
  for (let i=0; i<10; i++){
    let i = 'abc';
    console.log(i);
  }
  ```   
  需要区分"暂时性死区"  
  ``` js
  var tmp = 123
  if(true){
    tmp = 'abc';//报错:ReferenceError
    let tmp;
  }
  ```  
在代码块内，使用let命令声明变量之前，该变量都是不可用的。这在语法上，称为“暂时性死区”
- var命令会发生”变量提升“现象，即变量可以在声明之前使用，值为undefined  
let命令改变了语法行为，它所声明的变量一定要在声明后使用，否则报错
- let不允许在相同作用域内，重复声明同一个变量  
- 为什么需要块级作用域? 
第一种场景，内层变量可能会覆盖外层变量  
第二种场景，用来计数的循环变量泄露为全局变量  
- const声明一个只读的常量。一旦声明，常量的值就不能改变  
const声明的变量不得改变值，这意味着，const一旦声明变量，就必须立即初始化，不能留到以后赋值  
const的作用域与let命令相同：只在声明所在的块级作用域内有效
- const本质:  
对于简单类型的数据（数值、字符串、布尔值），值就保存在变量指向的那个内存地址，因此等同于常量。  
但对于复合类型的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指针，const只能保证这个指针是固定的，至于它指向的数据结构是不是可变的，就完全不能控制了
- ES5 只有两种声明变量的方法：var命令和function命令。ES6 除了添加let和const命令，后面章节还会提到，另外两种声明变量的方法：import命令和class命令。所以，ES6 一共有 6 种声明变量的方法
- 顶层对象: ES6 为了改变这一点，一方面规定，为了保持兼容性，var命令和function命令声明的全局变量，依旧是顶层对象的属性；  
另一方面规定，let命令、const命令、class命令声明的全局变量，不属于顶层对象的属性
  ``` js
  let a = 1;
  window.a; //undefined
  // 控制台也是类似的结果:
  a; // a is not defined ( 原因就是a不是顶层元素了! )
  ```
# Class语法
- 示例：  
  ``` js
  class Point {
    //构造方法
    //一个类里面, 只能有一个构造方法
    constructor(x, y) {
      //this关键字代表实例对象
      this.x = x;
      this.y = y;
    }

    //注意， 定义类的方法时， 前面不需要加function关键字
    toString() {
      return this.x + "," + this.y;
    }
  }

  var p = new Point(3, 5);
  ```
  等效写法  
  ``` js
  var Point = function(x, y) {
    this.x = x;
    this.y = y;
  }

  Point.prototype.toString = function(){
    return this.x + "," + this.y;
  };

  var p = new Point(2, 3);
  ```
- constructor方法是类的默认方法， 通过new命令生辰搞对象实例时，自动调用该方法。  
一个类必须有constructor方法，如果没有显式定义，一个空的constructor方法会被默认添加。
- 实例的属性除非显式的定义在其本身上（即定义在this对象上），否则都是定义在原型上（即定义在class上）  
实例：
  ``` js
  class Point {
    constructor(x, y) {
      this.x = x;
      this.y = y;
    }
    toString() {
      return this.x + "," + this.y;
    }
  }

  var point = new Point();
  point.hasOwnProperty('x')//为true
  point.hasOwnProperty('y')//为true
  point.hasOwnProperty('toString')//为false
  point._proto_.hasOwnProperty('toString')//为true
  ```
- Class表达式  
  ``` js
  const MyClass = class me {

  }
  ```
  上面表达式定义了一个类，但是需要注意的是，这个类的名字是MyClass而不是Me, Me只在内部代码可用， 指代当前类。  
  另外一种写法是：  
  ``` js
  const MyClass = class {}
  ```
  立即执行的类：  
  ``` js
  let person = new class {
    constructor(name){
      this.name = name;
    }
    sayName(){
      console.log("名字"+this.name);
    }
  }('张三');
  person.sayName();
  ```
  在这个立即执行的类中， 调用了构造方法！
- Class的取值函数getter和存值函数setter  
  关键字：get set  
  作用: 拦截该属性的存取行为  
  ``` js
  class MyClass {
    get prop(){
      console.log("执行字段prop的取值函数getter");
    }
    set prop(value){
      console.log("执行字段prop的存值函数setter,存的值是"+value);
    }
  }
  let mc = new MyClass();
  mc.prop(123);//打印
  mc.prop;//打印  
  ```
  实际使用中发现， 一旦添加setter方法后，这个字段再获取其值时，一定调用的是getter方法， 如果这个字段的getter方法没有，返回undefined
- Class的静态方法
  ``` js
  class Foo {
    static classMethod() {
      return 'hello';
    }
  }
  Foo.classMethod();//一定需要通过类调用， 而不能通过类的
  实例调用，会报异常  
  ```
- Class不支持私有方法/私有属性  
  支持 静态属性, 但是写法是: Point.staticPorp = 1; :
  ``` js
    class Class {
      static staticProp = 1; // 不支持等于号!SyntaxError: Unexpected token =
    }
    //静态属性 正确应该是:
    Class.staticProp = 1;

    //类定义属性:
    Class.prototype.prop = 2;
  ```

# Class 的继承
- 示例:
  ```  js
  class ColorPoint extends Point{
    constructor(x, y, color){
      //子类必须在constructor构造方法中调用super方法
      super(x, y);
      this.color = color;
    }

    toString(){
      return this.color + "," + super.toString();
    }
  }
  ```
  - 当子类有constructor构造方法时, constructor构造方法一定要调用super方法  
    如果子类没有constructor方法时, 就不需要掉用了, 不会报错
  - 在子类的constructor方法内部, 在没有调用super方法之前, 都不能使用this!  
    ``` js
    constructor(x, y, color){
      this.x = x; // ReferenceError
      super(x, y);
      this.y = y;
    }
    ```
- 父类的静态方法，也会被子类继承
- let cp = new ColorPoint();  
  cp instanceof ColorPoint // true  
  cp instanceof Point // true  
-   
  - super作为方法时, super()方法只能用于子类的constructor构造方法中, 不能用于其他地方
    ``` js
    class sub extends father{
      toStringss(){
        super(); // 报错
      }
    }
    ```
  - 当super作为对象时, 可以在其他方法内使用
    ``` js
    toString(){
      super.toString();
    }
    ```
  - 注意: 由于super指向的是父类的原型对象, 所以定义在父类实例上的方法或属性, 是无法通过super调用的:
    ``` js
    class Father {
      constructor(x){
        this.x = x;
      }
    }

    class Sub extends Father {
      toString(){
        super.x; // undefined
      }
    }
    ```
    - 如果属性定义在父类的原型对象上，super就可以取到
      ``` js
      class A {}
      A.prototype.x = 2;

      class B extends A {
        toString(){
          console.log(super.x);
        }
      }
- ES6规定, 在子类普通方法中通过super调用父类的方法时，方法内部的this指向当前的子类实例  
  由于this指向子类实例，所以如果通过super对某个属性赋值，这时super就是this，赋值的属性会变成子类实例的属性
- 其他还有很多继承的细节, 因为觉得少用, 以及和Java不同, 暂时不再

# Module语法
- ES6模块功能主要由两个命令构成: **export** 和 **import**  
  ```export```命令用于规定模块的对外接口, ```import```命令用于输入其他模块提供的功能     
  ES6的模块加载方式是: " 编译时加载 "或者叫" 静态加载 ",   
  这种加载方式,是在编译时就完成模块加载, 可以做到加载部分方法, 其他方法不加载, 效率也比较高
## export 命令
- 一个模块就是一个独立的文件.该文件内部的所有变量, 外部都无法获取. 如果希望外部能够读取内部的某个变量, 就必须使用export关键字输出该变量.  
  ``` js
  // 直接 export 一个变量声明的表达式
  export var firstName = 'Michael';
  export var year  = 2018;
  ```
  另外一种写法, 这种方式优先:  
  ``` js
  // 先定义变量, 方法
  var firstName = 'Michael';
  var year = 2018;
  // 然后export, 这种export, 此时需要花括号{}括起来
  // 同时这个写法, 有一次export多个模块接口的含义
  // 这样不需要多次使用export命令了
  export {firstName, year};
  ```
- 输出函数或类
  ``` js
  // export 一个方法声明的表达式
  // 不能是匿名方法
  export function multiply(x,y){};
  ```
- 输出重命名, 使用关键字 as
  ``` js
  function nameA(){};
  function nameB(){};
  export {
    nameA as renameA,
    nameB as renameB
  };
  ```
## import 命令
- import示例:
  ``` js
  import {firstName, year as renameYear} from './profile.js';  
  ```
  从./profile.js文件, 输入变量. 大括号里面指定从其他模块导入的变量名, 必须与被导入模块对外接口的名称相同  
  可以使用关键字 as 将输入的变量重命名  
  import输入的变量都是只读的, 不可重新赋值. 但是如果输入的变量是个对象, 那它的属性值是可以改变的:
  ``` js
  import {a} from './profile.js';
  a = {}; // 异常, a是只读的!
  a.foo = 'hello'; // 合法操作, 但是不建议!, 其他模块也可以读到改写后的值
  ```
  import后的from指定模块文件的位置, 可以是相对路径, 也可以是绝对路径, .js后缀可以省略
- import命令是编译阶段执行的, 在代码运行之前  
  ``` js
  foo();
  import {foo} from 'my_module';
  ```
  由于import是静态执行的, 所以不能使用表达式和变量, 这些只有在运行时才能得到结果的语法结构  
  ``` js
  import {'f'+'oo'} from 'my_module'; //错误

  let module = 'my_module';
  import {foo} from module; //错误

  if (x == 1){
    import {foo} from module; //错误
  }
  ```
- import执行细节:
  ``` js
  import 'lodash';
  import 'lodash';
  ```
  上面的代码加载了两次lodash, 但是只会执行一次  
  ``` js
  import {foo} from 'my_module';
  import {bar} from 'my_module';

  //等同于
  import {foo, bar} from 'my_module';
  ```
- 整体加载:  
  即用星号 * 指定一个对象, 所有输出值都会加载在这个对象上面

  感觉除了"整体加载 和 `export default`", 其他import 都需要花括号{}括一下 !
  ``` js
  import * as circle from './circle';

  //area circumference是模块circle的外部接口!
  circle.area(4);
  circle.circumference(14);
  ```

## export default 命令 
  * 从前面的例子可以看出, 使用```import```命令的时候, 用户需要知道所有加载的变量名或函数名, 否则无法加载  
    为了方便, 可以使用``` export default ``` 命令, 为模块指定默认输出.  
    ``` js
      // export 部分
      // lib.js
      funtion test1() ()
      export default test1;
      // lib2.js
      export default function() { // 可以是匿名方法, 也可以不是匿名方法 (会视为匿名)
        ;// doSomething()
      }

      //import 部分 (这里专指对应export default的import)
      // export default 输出的模块, 在import时, 不需要使用花括号{} !!
      // 这时的import, 系统允许你为它取任意名字
      import newModuleName from './lib.js'
    ```
    * ```export default```命令用于指定模块的默认输出  
      显然,一个模块只能有一个默认输出, 因此, 一个模块只能使用一次```export default```命令  
      ( 所以export default对应的import不需要花括号{}, 因为只可能唯一对应export default命令 )
## Module的加载实现
  * 浏览器加载ES6模块:
    ``` html
      <script type="module" scr="./foo.j"></script>
    ```
    浏览器对于带有```type="module"```的```<script>```, 都是异步加载, 不会造成堵塞浏览器, 即等到整个浏览器渲染完, 再执行模块脚本..  
    其他省略了, 毕竟现在应该不会用浏览器加载es6吧, 逃`
  - 在支持ES6的浏览器中使用 import 和 export 示例:    
    HTML, 注意, 使用了import或export, script标签都需要是: type="module"
    ``` html
    <script type="module" src="./script.js"></script>
    <script type="module">
        import * from './test.js'
    </script>
    ```
    script.js: type="module"
    ``` js
    import {area} from './circle.js';
    area();
    ```
    circle.js: type="module"
    ``` js
    var area = function(){
      console.log("Hello Import!");
    };
    export {area}
    ```
  * ES6模块和CommonJS模块的重大差异之一, **ES6模块输出的是值的引用**
    ``` js
      // lib.js
      export let counter = 3;
      export function incCounter() {
        counter++;
      }

      // main.js
      import { counter, incCounter } from './lib.js'
      console.log(counter); // -> 3
      incCounter();
      console.log(counter); // -> 4 
    ```
    最后的输出结果是4, 表示ES6模块输入的变量counter是活的, 完全反应在其所在模块lib.js内容的变化.   
    由于ES6输入的模块变量, 只是一个"符号连接", 所以这个变量是只读的, 对它进行重新赋值会报错.   
    export通过接口, 输出的是同一个值. 不同的脚本加载这个接口, 得到的都是同样的实例.  
    个人理解, ```import```导入的变量, 是个指针( 引用 ), 指向的是```export```输出的对象
  * ```export default```命令其实只是输出一个叫做`default`的变量, 所以它后面不能跟变量声明语句:  
    ``` js
      // 正确
      let a = 1;
      export default a;
      // 错误!!
      export default var sss = 1;
      // 方法声明语句是可以的, orz
    ```
  * Node 加载
    * 如果模块名不含路径, 那么import命令会去`node_modules`目录寻找这个模块  
      如: `import Vue from 'vue'`, 这个'vue'就不含路径, 他是在node_modules目录下的
    * 如果模块名包含路径, 那么import命令会按照路径去寻找这个名字的脚本文件. (支持file:协议)  
      `import 'file:///etc/config/app.json'`  
      `import './router/index.js'`
      * 如果脚本文件省略了后缀名, 比如`import './router'`, Node会依此尝试四个后缀名:`./router.mjs` `./router.js` `./router.json` `./router.node`   
      (这时依旧把路径当为"文件"处理, 因此给它加文件后缀尝试获取下)
        * 如果这些脚本文件都不存在, Node就会加载`./router/package.json`的`main`字段指定的脚本. 
          * 如果`./router/package.json`不存在或者没有`main`字段, 那么就会依此加载`./router/index.mjs` `./router/index.js` `./router/index.json` `./router/index.node`    
          (这是已经把路径当为"文件夹"处理了, 开始寻找这个文件夹下固定几个文件)  
          如果这四个文件还是都不存在, 就会抛出错误.   
    * 这样就可以解释加载路由或者vuex:
      ``` js
      import router from './router'
      // 等效:
      import router from './router/index.js'
      
      import store from './store'
      //等效
      import store from './store/index.js'
      ```
    * 还是没有理解:  
      `import App from './App'`, ./App是App.vue, vue后缀?


  * 关于import命令from后是一个文件夹, 然后就会查早这个文件夹下的index.js文件  
  或者说 ```import * as allModule from './folder/index.js'```, 路径把index.js给省略了...  
  从index.js文件里面查找export的部分  
  这部分好像是webpack做的?  
  ???
    
    
## import() 动态加载
  * ```import```命令会被JavaScript引擎静态分析, 先于模块内的其他语句执行 (`import`命令叫做"连接"binding其实更合适)  
  这样固然有利于编译器提高效率, 但同时导致无法在运行时加载模块, 使得条件加载不可能实现
  * 示例:
    ``` js
    const mian = document.querySelector('main')
    
    // import的参数, 是指定索要加载的模块的位置
    // 相当于import命令的from后面部分
    import(`./section-modules/${someVariable}.js`)
      // import()加载模块成功后调用then方法
      // then方法的参数是一个方法,
      // then参数对应的方法的参数就是 所要加载模块的接口! 
      // 就是原先import命令后的部分
      .then( function(module) {
        module.loadPageInfo(main);
      })
      .catch(err => {
        main.textContent = err.message;
      })
    ```
  * `import()`函数可以运行在任何地方, 不仅仅是模块, "非模块的脚本"也可以使用 (比如`type != "module"` 的script标签).  
    `import()`是异步执行的, 这点不同于Node的`require`方法
  * 使用场景  
    * 按需加载
    * 条件加载 (和按需加载类似, 都是我需要模块的时候, 在加载import()模块)
    * 动态的模块路径
      ``` js
        // f() 返回模块路径!
        import(f())
          .then( function(module) {
            console.log("加载的模块: " + module);
          });
      ```
# 变量的解构赋值
## 数组的解构赋值
  * 模式匹配
    `let [foo, [[bar], baz]] = [1, [[2], 3]];`  
    `let [, , y] = [1, 2, 3];`  
    解构不成功, 对应的值会等于undefined: (等式左边某个数组变量数量 大于 等式右边对应数组数量) 
    ``` js
    let [foo] = [];
    let [foo, bar] = [1];
    // c没有解构, c=undefined
    // 虽然中间c没有解构, 但是没有影响d的解构 !
    let [a, [b, c], d] = [1, [2], 4]; // a=1, b=2, c=undefined, d=4
    ```
    不完全解构, 即等号左边的模式只匹配一部分的等号右边的数组 (等式左边某个数组变量数量 小于 等式右边对应数组数量)  
    ``` js
    let [a, [b], c] = [1, [2, 3], 4];
    ```
    * 如果等式右边不是数组 (确切来说不是可遍历的结构), 那么将会报错:  
      `let [a] = 1;` `let [a] = {} `  
      注意对象也是不可遍历的结构 (虽然可以foreach)

* 默认值
  * 基本用法
    ``` js
    let [foo = true] = []; // foo = true
    let [x, y='b'] = ['a']; // x='a'  y = 'b'
    ```
    注意ES6使用严格相等运算符===, 判断一个位置是否有值, 因此, 只有当一个数组成员严格等于undefined, 默认值才会生效. 
    ``` js
    let [x = 1] = [undefined]; // x = `;
    let [x = 2] = [null]; // x = null !, 因为null 不严格等于 undefined
    ```
    * 默认值时惰性求值的, 即只有在用到的时候, 才会求值: 
      ``` js
      function f() { console.log("aaa"); return 5; };
      let [x = f()] = [1];
      ```
      因为数值成员 x 有解构匹配的值 1 了, 因此不需要在执行默认值函数, 因此f()方法的console没有打印  
      个人理解, 或表示: || 也是类似"惰性求值"吧. 如果||左边表达式结果为true, 那||后边的表达式就不执行了.
  * 对象的解构赋值
    ``` js
    let {bar, foo} = {bar: 'aaa', foo: 'bbb'};
    ```
    对象的解构与数组有一个重要的不同. 数组元素是按次序排列的, 变量的取值由它的位置决定; 而对象的属性没有次序, 变量必须与属性同名, 才能取到正确的值.   
    ``` js
    let {baz} = {bar: 'aaa', foo: 'bbb'}; // baz = undefined
    ```
    如果变量名与属性名不一致, 必须写成下面这样: 
    ``` js
    let obj = { first: 'hello', second: 'world' };
    let {first: ff, second: ss} = obj;
    // ff = 'hello'
    // ss = 'world'
    ```  
    这实际说明, 对象的解构赋值是下面形式的简写: 
    ``` js
    let {foo: foo, bar: bar} = {foo: 'aa', bar: 'bb'};
    let {foo: baz}  = {foo: 'aa'};
    // baz = 'aa'
    // foo? undefined
    // 上面的代码中, foo才是匹配的模式! baz才是真正的变量!
    // 通过属性名foo找到匹配的结果, 然后赋值给baz
    ```
    因此: **对象的解构赋值的内部机制, 是先找到同名属性, 然后再赋给对应的变量. 真正被赋值的是后者, 而不是前者 (注意属性名和变量名相同时可以省略的)** 



# 函数的扩展
* ## 箭头函数


