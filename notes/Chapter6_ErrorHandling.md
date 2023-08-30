* Rust中分为可恢复错误跟不可恢复错误两种。不可恢复错误有：`panic`、`断言`、`未实现的代码`、`不应当被访问的代码`。
    ```Rust
    fn add(a: u32, b: u32) -> u32 {
        unimplemented!()
    }
    fn sub(a: u32, b: u32) -> u32 {
        unreachable!()
    }

    fn main() {
        // panic! 宏
        // panic!("error");

        // 断言 assert!
        // assert!(1 == 2);
        // assert_eq!(1, 2);
        // assert_ne!(1, 1);
        
        // 未实现的代码 unimplemented!
        // let x = add(1, 2);
        
        // 不可到达的代码 unreachable!
        // let x = sub(1, 2);
    }
    ```
* 可恢复错误可以用Result配合枚举来处理。
    ```Rust
    let result = std::fs::read("/tmp/foo");
    match result {
        Ok(data) => println!("{:?}", std::str::from_utf8(&data).unwrap()),
        Err(err) => println!("{:?}", err),
    }
    ```

* 在用Result来传递错误的时候可以使用`?`关键字，直接向上层传递错误。其中值的类型可以不一样，但是错误的类型必须一样。
    ```Rust
    // 调用bar的地方可以使用`?`把错误抛出去
    fn bar() -> Result<u32, &'static str> {
        Ok(0)
    }

    // 返回的result，第一个参数可以不一样，但是第二个参数必须一样
    fn foo() -> Result<i32, &'static str> {
        let a = bar()?;
        Ok(a as i32)
    }

    fn main() {
        println!("{:?}", foo());
    }
    ```

* 自定义错误语法如下：
    ```Rust
    #[derive(Debug)]
    enum Error {
        IO(std::io::ErrorKind),
    }

    impl From<std::io::Error> for Error {
        fn from(error: std::io::Error) -> Self {
            Error::IO(error.kind())
        }
    }

    fn do_read_file() -> Result<(), Error> {
        let data = std::fs::read("/tmp/foo")?;
        let data_str = std::str::from_utf8(&data).unwrap();
        println!("{:?}", data_str);
        Ok(())
    }

    // main函数也能返回result
    fn main() -> Result<(), Error> {
        do_read_file()?;
        Ok(())
    }
    ```