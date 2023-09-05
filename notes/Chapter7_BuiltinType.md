* Box允许将一个值放在堆上而不是栈上，留在栈上的是只想堆数据的指针。Box是一个指向堆的智能指针，当一个Box超出作用域时，它的析构函数被调用，内部对象被销毁，堆上的内存被释放。

* Box没有运行上的性能损失，但是它只在以下场景比起默认的栈上分配更适用：
    - 当有一个在编译时位置大小的类型，而又想要在需要确切大小的上下文中使用这个类型值的时候。
    - 当有大量数据并希望在确保数据不被拷贝的情况下转移所有权的时候。
    - 当希望拥有一个值并只关心它的类型是否实现了特定trait，而不是其具体类型的时候。
    ```Rust
    // 错误案例
    enum List {
        Cons(i32, List),    // 报错。因为这是一个递归结构，最终的数据大小时不确定的，所以这时候需要用Box去包装List。
        Nil,
    }
    // 正确案例
    enum List {
        Cons(i32, Box<List>),
        Nil,
    }

    fn main() {
        let list = List::Cons(0, Box::new(List::Cons(1, Box::new(List::Nil))));
    }
    ```
    ```Rust
    fn main () {
        let a = [0: 1024 * 512]; // a存在栈上
        let a_box = Box::new(a); // 将a在栈上的数据拷贝到堆上
    }
    ```
    ```Rust
    fn main() -> Result<(), Box<dyn std::error::Error>> {
        let f = std::fs::read("/tmp/not_exist")?;
        Ok(())
    }
    ```

* Rc是Box的高级版本：它是带引用计数的智能指针。当它的引用计数为0时，数据才会被清理。比如下面这个例子，节点4拥有多个所有者，这个时候就需要使用Rc来进行包装。
    ```Rust
    // 0 -> 1 \
    //         | ->4
    // 2 -> 3 /
    use std::rc::Rc;

    enum List {
        Cons(i32, Rc<List>),
        Nil,
    }

    fn main() {
        let four = Rc::new(List::Cons(4, Rc::new(List::Nil)));

        let zero_one = List::Cons(0, Rc::new(List::Cons(1, Rc::clone(&four)))); // 使用Rc::clone()方法去触发rc的计数器加一
        let two_three = List::Cons(2, Rc::new(List::Cons(3, Rc::clone(&four))));
    }
    ```

* Vector是动态大小的数组，与切片一样，它们的大小在编译的时候是未知的，但它们可以随时增长或收缩。向量使用3个参数表示：指向数据的指针、长度、容量。
    ```Rust
    fn main() {
        // let mut v: Vec<i32> = Vec::new();
        // for i in 0..10 {
        //     v.push(i);
        // }
        let mut v: Vec<i32> = vec![0, 1, 2, 3, 4, 5, 6, 7, 8, 9];

        for e in v.iter_mut() {
            *e *= 2;
        }

        for i in 0..v.len() {
            println!("v[{:?}]={:?}", i, v[i])
        }
    }
    ```

* 大多数数据类型都可以作为HashMap的Key，只要它们实现了Eq和Hash的traits。
    ```Rust
    use std::collections::HashMap;

    fn main() {
        let mut transcript: HashMap<&str, u32> = HashMap::new();
        transcript.insert("alice", 95);

        match transcript.get(&"alice") {
            Some(data) => println!("alice {:?}", data),
            None => println!("alice not found"),
        }

        for (&name, &score) in transcript.iter() {
            println!("{:?} {:?}", name, score)
        }
    }
    ```

* Rust中的字符串主要用到的有两种，分别是`&str`和`String`。第一种主要用在函数的参数传递上，另一种用在类型声明上。
  
* str代表的是内存中（数据段、代码段、堆、栈）的字符串数据。而&str可以引用数据段中的内容，也可以引用堆里面的内容。

* String类型的数据拥有自己的数据（存放在堆中），并且可修改。而&str只是引用。