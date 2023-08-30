* Rust项目的代码包含三个基本概念：Package（包）、Crate（箱）、Module（模块）。

* Package用于管理一个或多个Crate。Package的创建方式就是`cargo new`。

* 一个package的基本结构如下：
    ```
    package name
    ├─src
    │  └─main.rs
    └─Cargo.toml
    ```

* 默认情况下`src/main.rs`是与package同名的二进制Crate的入口文件。所以我们可以说我们现在有一个`my-project package`以及一个二进制`my-project crate`。

* 如果我们在创建package的时候带上`--lib`，那么`src/lib.rs`将是`Crate`入口文件，且他是一个库的crate。

* 如果我们的src目录中同时包含`main.rs`和`lib.rs`，那么我们将在这个Package中同时得到一个二进制Crate和一个库Crate。

* Crate是Rust的最小编译单元，即Rust编译器是以Crate为最小单元进行编译的。Crate在一个范围内将相关的功能组合在一起，并最终通过编译生成一个二进制或者库文件。

* Module允许我们将一个Crate中的代码组织成独立的代码块，以便于增强可读性与代码复用。同时Module可以控制代码的可见性，即将代码分为公开代码和私有代码。不声明的情况下，默认为私有的。
    ```Rust
    mod mod1 {
        pub mod mod2 {
            pub const MESSAGE: &str = "hello world!";
        }
    }

    fn main() {
        println!("{}",mod1::mod2::MESSAGE);
    }
    ```

* 常见的三种pub写法：
    - pub：成员对模块可见。
    - pub(self)：成员对模块内的子模块可见。（没有作用，不加也行）
    - pub(crate)：成员对整个crate可见。
    ```Rust
    mod mod1 {
        pub(crate) enum CrateEnum {
            Item = 4
        }
    }
    fn main() {
        println!("{}",mod1::CrateEnum::Item as u32);
    }
    ```

* 结构体中的字段和方法默认是私有的，通过加`pub`关键字可以让结构体中的字段和方法公有。注意，与结构体同意模块的代码访问结构体中的字段和方法并不要求该字段是可见的。
    ```Rust
    mod mod1 {
        pub struct Person {
            pub name: String,
            nickname: String,
        }

        impl Person {
            pub fn new(name: &str) -> Self {
                Person { 
                    name: String::from(name),
                    nickname: String::new()
                }
            }

            pub fn set_nickname(&mut self, nickname: &str) {
                self.nickname = String::from(nickname);
            }

            pub fn say_nickname(&self) {
                println!("{}", self.nickname);
            }
        }
    }

    fn main() {
        let mut p: mod1::Person = mod1::Person::new("jack");
        p.set_nickname("baby");
        p.say_nickname();
    }
    ```

* `use`关键字的使用方法：
    ```Rust
    // use std::fs::read;
    // use std::fs;
    use std::fs as stdfs;

    fn main() {
        // 没有使用use导入
        // let data = std::fs::read("src/main.rs").unwrap();
        // 使用`use std::fs::read`导入
        // let data = read("src/main.rs").unwrap();
        // 使用`use std::fs`导入
        // let data = fs::read("src/main.rs").unwrap();
        // 使用`use std::fs as stdfs`导入
        let data: Vec<u8> = stdfs::read("src/main.rs").unwrap();
        println!("{}", String::from_utf8(data).unwrap());
    }
    ```

* 除了可以使用完整路径访问模块内的成员，还可以用`super`与`self`关键字来使用相对路径进行访问。super是上层模块，self是当前模块。

* 将模块映射到文件。我们可以使用`mode <path>`语法，将rust源码文件作为模块引入。  
    ```Rust
    /*
    当前目录结构如下：
    src
    ├─main.rs
    ├─mod1.rs
    */

    // mod1.rs代码如下
    pub const MESSAGE: &str = "hello world";

    // main.rs代码如下
    mod mod1;
    fun main() {
        println!("{}", mod1::MESSAGE);
    }
    ```

* 将模块映射到文件夹。当一个文件夹包含`mod.rs`文件时，该文件夹可以被作为一个模块。
    ```Rust
    /*
    文件目录结构如下
    src
    ├─mod1
    │  └─mod.rs
    └─main.rs
    */
    // mode1/mod.rs代码如下
    pub const MESSAGE: &str = "hello world!";
    // main.rs代码如下
    mod mod1;

    fn main() {
        println!("{}", mod1::MESSAGE);
    }
    ```
