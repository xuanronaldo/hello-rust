* 放在栈里面的数据是`值类型数据`（比如int型），放在堆里面的数据是`引用类型数据`（比如string类型）。

* 所有权的实际规则如下：
    - Rust中每个值都绑定有一个变量，称为该值的所有者。
    - 每个值只有一个所有者，而且每个值都有它的作用域。
    - 一旦当这个值离开作用域，这个值占用的内存将被回收。
    ```Rust
    {
        let value = 1;
    }
    println!("{}", value); //报错，已经出了value的作用域
    ```
    ```Rust
    let s1 = String::from("hello world!");
    let s2 = s1;
    println!("{}", s1); // 报错，当前字符串的所有权已经转移给s2了。
    ```
    ```Rust
    let s2: String;
    {
        let s1 = String::from("hello world!");
        s2 = s1;
    }
    println("{}", s2); // 可以正常运行。
    ```
    ```Rust
    fn echo(s: String) {
        println!("{}", s);
    }

    fn main() {
        let s = String::from("hello world");
        echo(s);
        println!("{}", s);  // 报错，因为所有权已经转移到echo函数里面了
    }
    ```
    ```Rust
    fn echo(s: &String) {
        println!("{}", s);
    }

    fn main() {
        let s = String::from("hello world");
        echo(&s);
        println!("{}", s);  // 正常运行
    }
    ```

* 默认情况下，引用时不可变的，如果希望修改引用的值，需要使用`&mut`。 
    ```Rust
    fn change(s: &mut String) {
        s.push_str(" changed!");
    }

    fn main() {
        let mut s = String::from("hello world");
        change(&mut s);
        println!("{}", s);
    }
    ```

* 一个变量在同一时间，最多只能存在一个可变引用。此规则主要用于防止数据竞争。
    ```Rust
    let s = String::from("hello world!");
    let s1_ref = &mut s;
    let s2_ref = &mut s; // 报错
    ```

* 当我们在与`&str`交互的时候，我们经常需要用`'a`去手动注明生命周期。生命周期注解并不会改变任何引用的生命周期的长短。
    ```Rust
    fn bigger<'a>(str1: &'a str, str2: &'a str) -> &'a str {
        if str1 > str2 {
            str1
        } else {
            str2
        }
    }

    fn main() {
        println!("{}", bigger("a", "b"));
    }
    ```
    ```Rust
    #[derive(Debug)]

    struct Person<'a> {
        name: &'a str,
    }

    fn main() {
        let p: Person<'_> = Person {name: "jack"};
        println!("{:?}", p);
    }
    ```