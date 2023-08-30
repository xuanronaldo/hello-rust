* 类型分类
    ```
    静态类型：在编译期对类型进行检查
    动态类型：在运行期对类型进行检查
    强类型：不允许隐式类型转换
    弱类型：允许隐式类型转换
    ```

* `C语言`是静态弱类型语言，`Python`是动态强类型语言，`Rust`是静态强类型语言。

* `宏`：可变参数的函数。调用时需要在括号前加`!`。
    ```Rust
    println!("The value of x is {}", x);
    ```

* 变量默认是不可变的。如果需要可变的话需要在变量名前加`mut`
    ```Rust
    let mut x = 5;
    x = 6;
    ```

* 常量用`const`关键字定义，常量在编译期间求值，不可变变量在运行间求值。
    ```Rust
    const A_CONST: i32 = 42;
    ```

* shadowing：隐藏（推荐使用，不推荐使用可变变量）
    ```Rust
    let x = 5;
    let x = x + 1;
    ```

* 未明确数据类型的整数，默认使用`i32`。

* 未明确数据类型的小数，默认使用`f64`。

* Rust中的字符类型是`UTF-8`编码。

* Rust中的元组类型中的元素数据类型不必相同。
    ```Rust
    let a: i32 = 10;
    let b: char = 'A';
    let mytuple: (i32, char) = (a, b)
    let (c, d) = mytuple;   // 使用方式1：解封装

    println!("{}", mytuple.0)   // 使用方式2：取单个值
    ```

* Rust中的元组，最广泛的应用就是作为函数的返回值。
    ```Rust
    let a: i32 = 10;
    let (result, is_overflow) = a.overflowing_add(10);
    println!("result: {}, is_overflow: {}", result, is_overflow);
    ```

* 数组中的元素数据类型必须一致，并且数组长度不可变，编译期间会对数组索引进行越界检查，如果越界会编译报错。如果数组索引是后面计算出来的，则会运行报错。
    ```Rust
    let myarray: [u32; 5] = [1, 2, 3, 4, 5];
    println!("myarray[5] = {}", myarray[5]);    // 编译报错
    let index = "5".parse::<usize>().unwrap();
    println!("myarray[5] = {}", myarray[index]);    // 运行报错
    ```

* 如果数组每个元素都一样，可以用如下方式来初始化:
    ```Rust
    let myarray: [u32; 32 * 1024] = [0; 32 * 1024];
    println!("myarray[1024] = {}", myarray[1024]);
    ```

* 修改数组中的元素，使用如下方式：
    ```Rust
    let mut myarray: [u32; 5] = [0; 5];
    myarray[4] = 1;
    ```

* 切片类型是对一个数组的引用片段，能够安全有效地访问数组的一部分，而不需要拷贝数组或者数组中的内容。一个切片保存着两个usize成员，第一个指向起始位置的指针，第二个表示切片长度。数组的切片语法如下：
    ```Rust
    let arr: [i32; 5] = [1,2,3,4,5];    // `&`是引用符号，`..`是range语法。
    let slice = &arr[0..3];
    ```

* 去修改可变切片中的元素时，原来的可变数组中的元素也会改变。
    ```Rust
    let mut arr: [i32; 5] = [1,2,3,4,5];
    let slice = &mut arr[0..3];
    slice[0] = 6;
    println!("arr[0]={}", arr[0]);      // 输出结果是6
    ```

* 结构体与对象的区别：结构体是数据的集合，对象是数据与算法的结合。

* 结构体与元组类似，但是我们可以为结构体中的每个成员命名。

* 结构体中的三种类型： 
    ```Rust
    // 元组结构
    struct Pair(u32, f32);
    // C 结构
    struct Person {
        name: String,
        age: u8
    }

    // 单元结构（无字段，通常在泛型中使用）
    struct Unit;

    fn main() {
        let pair = Pair(10, 4.2);
        println!("{}", pair.0);

        let jack = Person {
            name: String::from("jack"),
            age: 6,
        };
        print!("{} {}", jack.name, jack.age);
        
        let unit = Unit;

    }
    ```

* 如何一次性打印一个结构体中的所有成员：
    ```Rust
    #[derive(Debug)]    // 派生属性
    struct Person {
        name: String,
        age: u8
    }

    fn main() {
        let jack = Person {
            name: String::from("jack"),
            age: 6,
        };
        print!("{:?}", jack);   // {}中需要使用`:?`
    }
    ```

* Rust中的枚举分为3种，枚举类型主要用于模式匹配。
    ```Rust
    // 无参枚举
    enum Planet {
        Mars,
        Earth,
    }
    // 带枚举值的枚举
    enum Color {
        Red = 0xff0000,
        Green = 0x00ff00,
        Blue = 0x0000ff,
    }
    // 带参数的枚举
    enum IPAddr {
        IPv4(u8, u8, u8, u8),
        IPv6(u8, u8, u8, u8, u8, u8, u8, u8, u8, u8, u8, u8, u8, u8, u8, u8),
    }
    ```

* 模式匹配用法如下：
  ```Rust
    enum IPAddr {
        IPv4(u8, u8, u8, u8),
        IPv6(u8, u8, u8, u8, u8, u8, u8, u8, u8, u8, u8, u8, u8, u8, u8, u8),
    }

    fn main() {
        let localhost: IPAddr = IPAddr::IPv4(127, 0, 0, 1);

        match localhost {
            IPAddr::IPv4(a, b, c, d) => {
                println!("{} {} {} {}", a, b, c, d)
            }
            _ => {}
        }
    }
  ```