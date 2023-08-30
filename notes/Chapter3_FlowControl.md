* 表达式与语句的区别是：表达式有返回值，语句没有返回值。

* Rust是基于表达式的语言，这意味着它的大多数代码都有一个返回值，除了以下几种语法:
    - 变量声明
    - 模块声明
    - 函数声明
    - 结构体声明
    - 枚举声明

* if表达式，类似于三目运算符：
    ```Rust
    let cond = true;
    let a = if cond {
        42
    } else {
        24
    };
    ```

* loop表达式的break语句后可以跟着一个返回值返回
    ```Rust
    let mut s = 0;
    let mut n = 10;
    let a = loop {
        if n < 0 {
            break s;
        }
        s += n;
        n -= 1;
    };
    println!("{}", a);
    ```

* while的语法如下：
    ```Rust
    let mut n = 1;
    while n < 101 {
        if n % 15 == 0 {
            println!("fizzbuzz");
        } else if n % 3 == 0 {
            println!("fizz");
        } else if n % 5 == 0 {
            println!("buzz");
        } else {
            println!("{}", n)
        }
        n += 1;
    }
    ```

* Rust中的range语法如下：
    ```Rust
    for i in 0..5 {
        // 生成的i有：0，1，2，3，4
    }
    for i in 0..=5 {
        // 生成的i有：0，1，2，3，4，5
    }
    ```

* 用for循环去迭代数组：
    ```Rust
    let mut myarray = [1, 2, 3];
    for i in myarray.iter_mut() {   // iter_mut迭代器可以修改数组内容
        *i *= 2;
    }

    for i in myarray.iter() {
        println!("{}", i);
    }
    ```

* if let语法糖，是用来简化match操作的。如果我们仅仅想当匹配发生时做某些操作，就可以使用if let语法糖来代替match操作。
    ```Rust
    enum Alphabet {
        A,
        B,
    }

    fn main() {
        let letter = Alphabet::A;

        match letter {
            Alphabet::A => {
                println!("It's A");
            }
            _ => {}
        }

        if let Alphabet::A = letter {   // 与上面的match语句等效
            println!("It's A");
        }
    }
    ```

* while let语法糖。（很少被使用）
    ```Rust
    enum Alphabet {
        A,
        B,
    }
    fn main() {
        let mut letter = Alphabet::A;

        while let Alphabet::A = letter {
            println!("It's A");
            letter = Alphabet::B;
        }

    }  
    ```

* 函数的定义，以递归的斐波那契函数为例：
    ```Rust
    fn fibonacci(n: u64) -> u64 {
        if n < 2 {
            return n;
        }
        return fibonacci(n - 1) + fibonacci(n - 2);
    }
    ```

* 方法是附加到对象的函数，访问对象中的方法有两种方式，如果带self参数就用`.`，否则就用`::`。
    ```Rust
    #[derive(Debug)]
    struct Point {
        x: u64,
        y: u64,
    }

    impl Point {
        // 构造方法
        fn new(x: u64, y: u64) -> Point {
            Point { x, y }
        }

        fn get_x(&self) -> u64 {
            self.x
        }
        
        fn set_x(&mut self, x: u64) {
            self.x = x
        }

    }

    fn main() {
        let mut p = Point::new(10, 20);
        println!("{:?}", p);
        println!("{:?}", p.get_x());

        p.set_x(20);
        println!("{:?}", p.get_x());

    }
    ```

* 闭包是一种匿名函数，它可以从上下文捕获变量的值，闭包可以被保存在变量中。
    ```Rust
    let times3 = |n: u32| -> u32 { n * 3 };
    println!("{}", times3(10));
    ```

* 闭包中可以使用move关键字，将闭包外部的值移到内部。使用场景是在多线程中，将主线程中的值移动到子线程中。
    ```Rust
    use std::thread;
    fn main() {
        let message = "Hello world!";

        thread::spawn(move || {
            println!("{}", message);
        }).join();
    }
    ```

* 高阶函数需要满足以下一个条件：
    - 接受一个或多个函数作为输入。
    - 输出一个函数。
    ```Rust
    // 接受一个或多个函数作为输入
    type Method = fn(u32, u32)->u32;
    fn calc(method: Method, a:u32, b:u32) -> u32 {
        method(a, b)
    }

    fn add(a:u32, b:u32) -> u32 {
        a + b
    }

    fn sub(a:u32, b:u32) -> u32 {
        a - b
    }

    fn main() {
        println!("{}", calc(add, 10, 20));
        println!("{}", calc(sub, 20, 10));
    }
    ```
    ```Rust
    // 输出一个函数
    type Method = fn(u32, u32)->u32;

    fn calc(method: &str) -> Method {
        match method {
            "add" => add,
            "sub" => sub,
            _ => unimplemented!(),
        }
    }

    fn add(a:u32, b:u32) -> u32 {
        a + b
    }

    fn sub(a:u32, b:u32) -> u32 {
        a - b
    }

    fn main() {
        println!("{}", calc("add")(10, 20));
        println!("{}", calc("sub")(20, 10));
    }
    ```

* 发散函数永远不会被返回，他们的返回值类型被标记为`!`，这是一个空类型。发散函数与空返回值函数不同，空返回值函数可以被返回。
    ```Rust
    // 发散函数
    fn foo() -> ! {
        panic!("This call never returns.");
    }
    // 空返回值函数
    fn some_fn() {
        ()
    }
  ```

* 使用本章内容完成`猜数字`游戏。
    ```Rust
    use std::io;
    use rand::Rng;

    fn main() {
        println!("Guess the number!");

        let secret_number: u32 = rand::thread_rng().gen_range(1..101);

        loop {
            // 获取用户输入
            println!("Please input your guess:");
            let mut guess = String::new();
            io::stdin().read_line(&mut guess).unwrap();

            let guess_number: u32 = match  guess.trim().parse() {
                Ok(num) => num,
                Err(err) => continue,
            };

            // 判断大小
            if guess_number > secret_number {
                println!("Too big");
            } else if guess_number < secret_number {
                println!("Too small");
            } else {
                println!("You win!");
                break;
            }
        }
    }
    ```
