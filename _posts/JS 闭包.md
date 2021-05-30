JS 闭包
------

闭包是函数和函数所声明地方的变量作用域的结合;      



#### 闭包

        function makeFunc() {
            let name = "Mozilla";
            function displayName() {
                console.log(`name = ${name}`); 
            }
            return displayName;
        }

        let myFunc = makeFunc();
        myFunc();


myFunc() 执行, 会输出字符串 Mozilla;     
displayName() 是内部函数, 但是在执行是, 作为返回值赋值给了内部函数, 仍然正常运行;  在部分语言中, 函数内部的局部变量, 仅在函数执行期间可用, 一旦函数执行完成, 内部局部变量将不可用;  但是上面代码, makeFunc() 执行过后，函数内部变量 name 仍然可以输出, 这个就是js闭包的效果;     

闭包是一种特殊的对象, 由两部分组成: 函数, 以及创建该函数时的环境. 环境由创建该函数时 在作用域中的所有局部变量组成;     

#### 闭包模拟对象及私有变量方法

JS 本身不支持私有方法和变量; 因为闭包拥有函数创建时 所处作用域的所有环境, 从而可以使用闭包来模拟私有属性;

        var Counter = (function() {
            var privateCounter = 0;
            function changeBy(val) {
                privateCounter += val;
            }

            return {
                increment: function() {
                    changeBy(1);
                },
                decrement: function() {
                    changeBy(-1);
                },
                value: function() {
                    return privateCounter;
                }
            }   
        })();

        console.log(Counter.value()); /* logs 0 */
        Counter.increment();
        Counter.increment();
        console.log(Counter.value()); /* logs 2 */
        Counter.decrement();
        console.log(Counter.value()); /* logs 1 */

上面代码, 创建了一个匿名函数, 并且理解调用, 将返回值赋值给了Counter; 由于是匿名函数调用执行, 外部无任何变量是指向当前匿名函数的, 从而外部无法修改调用 privateCounter 变量, 从而实现私有变量,  内部privateCounter变量修改, 只能通过返回的闭包修改, 从而实现了类似Java中的类和私有属性.

#### 常见错误, 循环中创建闭包

循环体中创建的闭包, 闭包创建时的作用域, 因此可以使用循环体中声明的变量, 而循环体中的变量 可能在循环的时候 值发生变化, 造成最终效果和预期的不一样;

        <p id="help">Helpful notes will appear here</p>
        <p>E-mail: <input type="text" id="email" name="email"></p>
        <p>Name: <input type="text" id="name" name="name"></p>
        <p>Age: <input type="text" id="age" name="age"></p>

        function showHelp(help) {
            document.getElementById('help').innerHTML = help;
        }

        function setupHelp() {
            var helpText = [
                {'id': 'email', 'help': 'Your e-mail address'},
                {'id': 'name', 'help': 'Your full name'},
                {'id': 'age', 'help': 'Your age (you must be over 16)'}
            ];

            for (var i = 0; i < helpText.length; i++) {
                var item = helpText[i];
                document.getElementById(item.id).onfocus = function() {
                    showHelp(item.help);
                }
            }
        }

        setupHelp();

上面代码, 无论点击哪个, 出现的都是年龄相关的信息;     
造成这个的原因, 就是循环体类, 创建的3个闭包, 每个都引用 item, 单循环体结束后, item指向最后一个变量, 3个闭包内部调用item时, 都是使用的最后指向的值;      

####### 解决办法
1. 使创建的多个闭包, 使用的是不用的环境, 从而不相互影响

        function showHelp(help) {
            document.getElementById('help').innerHTML = help;
        }

        function setupHelp() {
            var helpText = [
                {'id': 'email', 'help': 'Your e-mail address'},
                {'id': 'name', 'help': 'Your full name'},
                {'id': 'age', 'help': 'Your age (you must be over 16)'}
            ];

            for (var i = 0; i < helpText.length; i++) {
                (function() {
                    var item = helpText[i];
                    document.getElementById(item.id).onfocus = function() {
                        showHelp(item.help);
                    }
                })(); // Immediate event listener attachment with the current value of item (preserved until iteration).
            }
        }

        setupHelp();

    上面代码中, 在创建闭包的外面, 包裹了一个立即调用的匿名函数, 从而使每个闭包创建时的环境都不一样了, 从而使各个闭包内部使用的 item 值, 不会指向同一个位置;

    2. 使用let关键词
    var的作用域是整个全局的; 而let的作用域是当前块, 子块中的调用修改,不会影响子块外面的变量;     
    let类似于Java函数中的局部变量, 每个块中声明使用, 都会生成新变量; 不像var, var声明的变量, 会提升到当前上下文的顶部执行, 多次生声明赋值的var, 其实只是同一个变量, 多次赋值;

        function showHelp(help) {
            document.getElementById('help').innerHTML = help;
        }

        function setupHelp() {
            var helpText = [
                {'id': 'email', 'help': 'Your e-mail address'},
                {'id': 'name', 'help': 'Your full name'},
                {'id': 'age', 'help': 'Your age (you must be over 16)'}
            ];

            for (var i = 0; i < helpText.length; i++) {
                let item = helpText[i];
                document.getElementById(item.id).onfocus = function() {
                    showHelp(item.help);
                }
            }
        }

        setupHelp();
用let而不用var，使得每个闭包绑定块内变量，不需要额外的闭包, 可以降低内存上面的消耗;


#### 性能考量
闭包对性能有负面影响, 不是必须使用闭包时, 应该尽量减少闭包的使用;

eg. 使用函数模拟传统语言的类时, 方法通常应该关联于对象的原型，而不是定义到对象的构造器中。原因是这将导致每次构造器被调用，方法都会被重新赋值一次（也就是说，为每一个对象的创建）;

        function MyObject(name, message) {
            this.name = name.toString();
            this.message = message.toString();
            this.getName = function() {
                return this.name;
            };

            this.getMessage = function() {
                return this.message;
            };
        }

上面的代码并未利用到闭包的益处，因此，应该修改为如下常规形式：

        function MyObject(name, message) {
            this.name = name.toString();
            this.message = message.toString();
        }
        MyObject.prototype = {
            getName: function() {
                return this.name;
            },
            getMessage: function() {
                return this.message;
            }
        };

下面的代码可以更简洁的实现同样效果:

        function MyObject(name, message) {
            this.name = name.toString();
            this.message = message.toString();
        }
        (function() {
            this.getName = function() {
                return this.name;
            };
            this.getMessage = function() {
                return this.message;
            };
        }).call(MyObject.prototype);


------- 
参考: https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures



