+++
title = "ECS"
weight = 3
sort_by = "weight"
template = "book-section.html"
page_template = "book-section.html"
+++

Bevy中的所有应用程序逻辑都使用实体(Entity)组件(Component)系统(System)范式, 通常简称为ECS.  
ECS是一种软件模式, 包括将程序分解为**实体(Entity)**, **组件(Component)**和**系统(System)**.  
<span class="warning"> **实体(Entity)** 是指向一群 **组件(Component)** 的唯一 **事物(things)**, 然后使用 **系统(System)** 处理其过程.</span>

例如, 一个实体可能有`位置(Position)`和`速度(Velocity)`组件, 而另一个实体可能有`位置(Position)`和`UI`组件. 系统是在一组特定组件上运行的逻辑. 你可能有一个在所以组件上带`位置(Position)`和`速度(Velocity)`组件的`运动`系统.

ECS模式鼓励清晰, (解耦)分离的设计, 强制使你将数据和逻辑分解为核心组件. 通过优化内存访问模式并简化并行性, 有助于使你的代码更快.

## Bevy ECS

Bevy ECS是Bevy对ECS模式的实现. 不像其他需要复杂的生存期, 特征, 构建器模式或者宏的Rust ECS的实现, Bevy ECS对所有这些概念使用常规的Rust数据类型:

* **组件(Components)**: 普通的Rust结构体
    ```rs
    struct Position { x: f32, y: f32 }
    ```
* **系统(Systems)**: 普通的Rust函数
    ```rs
    fn print_position_system(position: &Position) {
        println!("position: {} {}", position.x, position.y);
    }
    ```
* **实体(Entities)**: 包含唯一整数的简单类型
    ```rs
    struct Entity(u64);
    ```

现在让我们在实践中看看它是如何工作的!

## 你的第一个系统(System)

拷贝下面的函数到你的`main.rs`文件中:

```rs
fn hello_world() {
    println!("hello world!");
}
```

这将是我们第一个系统. 剩下的步骤就是把它加到我们的App中!

```rs
fn main() {
    App::build()
        .add_system(hello_world.system())
        .run();
}
```

**注意**`hello_world.system()`函数的调用. 这是一种`特征扩展方法`, 可将`hello_world`函数转变成{{rust_type(type="trait" crate="bevy_ecs" name="System")}}类型.

调用{{rust_type(type="trait" crate="bevy_ecs", name="IntoQuerySystem" method="add_system" no_struct=true)}}方法把系统加到App的{{rust_type(type="struct", crate="bevy_ecs", name="Schedule")}}, 这个我们稍后再介绍.

现在我们再一次执行`cargo run`. 你将在命令行中看见`hello world!`的输出.

## 你的第一个组件(Components)

能向整个世界打招呼当然很好, 但是如果我们只想问候特定的人呢? 在ESC中, 通常会将人建模成实体, 并使用一些组件来定义它们. 让我们从一个`Person`的组件开始.

将这个结构体添加到`main.rs`:
```rs
struct Person;
```

但是, 如果我们希望人有一个名字? 在传统的设计中, 我们可能只是添加一个`name: String`的字段到`Person`中. 但其他实体也会拥有名字! 举个例子, 狗可能也应该有一个名字. 将数据类型分解成小块, 以鼓励代码重用通常更有意义. 因此, 让`名字(Name)`成为独立的组件.
```rs
struct Name(String);
```

我们可以通过`startup system`将`People`加到我们的世界({{rust_type(type="struct" crate="bevy_ecs" name="World")}}).  
`startup system`就像普通的系统(System)一样, 但它们只运行一次, 而且是在其他系统之前被调用.  
当我们的应用启动时, 让我们通过{{rust_type(type="struct" crate="bevy_ecs" name="Commands")}}生产大量的实体到我们的世界({{rust_type(type="struct" crate="bevy_ecs" name="World")}})中.

```rs
fn add_people(mut commands: Commands) {
    commands
        .spawn((Person, Name("Elaina Proctor".to_string())))
        .spawn((Person, Name("Renzo Hume".to_string())))
        .spawn((Person, Name("Zayna Nieves".to_string())));
}
```

像这样注册启动系统(startup system):

```rs
fn main() {
    App::build()
        .add_startup_system(add_people.system())
        .add_system(hello_world.system())
        .run();
}
```

我们现在可以运行这个应用, `add_people`系统将会最先执行, 然后是`hello_world`. 但是我们新的`people`仍然无事可做! 让我们创建一个系统, 让我们的新公民向我们的世界({{rust_type(type="struct" crate="bevy_ecs" name="World")}})打招呼:

```rs
fn greet_people(_person: &Person, name: &Name) {
    println!("hello {}!", name.0);
}
```

在我们的App中注册它:

```rs
fn main() {
    App::build()
        .add_startup_system(add_people.system())
        .add_system(hello_world.system())
        .add_system(greet_people.system())
        .run();
}
```

我们传递给`系统函数`的参数定义了系统运行在哪些实体上. 在这个例子中, `greet_people`将在所有拥有`Person`和`Name`组件的实体上运行.

现在我们运行我们的App, 将会得到以下结果:

```
hello world!
hello Elaina Proctor!
hello Renzo Hume!
hello Zayna Nieves!
```

棒棒哒!

备注: `hello world!`可能出现的顺序和上面显示的不同. 这是因为系统在没有共享依赖关系的情况下, 默认是并行的.
