+++
title = "插件"
weight = 4
sort_by = "weight"
template = "book-section.html"
page_template = "book-section.html"
+++

Bevy的核心原则之一就是模块化. 所以Bevy引擎的功能都是以插件实现的. 这包括像渲染器这样的内置功能, 但游戏本身也是作为插件来实现的! 这使得开发人员能够选择他们想要的功能. 不需要UI? 不要注册UI插件({{rust_type(type="struct" crate="bevy_ui", name="UiPlugin")}}). 想要一个无头服务(headless server)? 不要注册渲染插件({{rust_type(type="struct" crate="bevy_render" name="RenderPlugin")}}).

这也意味着您可以自由替换任何您不喜欢的组件。如果您觉得有必要, 欢迎您构建自己的{{rust_type(type="struct" crate="bevy_ui" name="UiPlugin")}}, 但是如果您认为它有用的话, 可以考虑将它[贡献给Bevy](/learn/book_cn/contributing)!

然而, 大部分开发者不需要自定义体验, 而只是想要没有麻烦的`全引擎`体验. 为此, Bevy提供了一组`默认插件`.


## Bevy的默认插件

通过添加`默认的Bevy插件`, 让我们的应用更加有趣.  
通过`add_plugins(DefaultPlugins)`添加了大多数人希望从引擎获得的特性, 比如 2D / 3D渲染器, 资源加载, UI系统, 窗口和输入.

```rs
fn main() {
    App::build()
        .add_plugins(DefaultPlugins)
        .add_startup_system(add_people.system())
        .add_system(hello_world.system())
        .add_system(greet_people.system())
        .run();
}
```

再一次执行`cargo run`.

你应该注意到两件事:
* **一个弹出的窗口**  
    这是因为我们现在有了窗口插件({{rust_type(type="struct" crate="bevy_window" name="WindowPlugin")}}), 它定义了窗口界面(但实际上并不知道如何制作窗口), 还有{{rust_type(type="struct" crate="bevy_winit" name="WinitPlugin")}}插件, 它使用[winit库](https://github.com/rust-windowing/winit)来使用系统本身的API窗口来创建窗口.
* **你的控制台现在全是`hello`的消息**  
    这是因为{{rust_type(type="struct" crate="bevy" name="DefaultPlugins")}}添加了一个`event loop(循环事件)`到我们的应用. 我们应用的ESC调度器在每一帧运行一次. 我们将在稍后解决控制台的垃圾信息.


备注: `add_plugins(DefaultPlugins)`等同于下面这段代码:
```rs
fn main() {
    App::build()
        .add_plugin(CorePlugin::default())
        .add_plugin(InputPlugin::default())
        .add_plugin(WindowPlugin::default())
        /* more plugins omitted for brevity */
        .run();
}
```

你可以自由的使用任何适合你的方法!

## 创建你的第一个插件

为了更好的管理, 让我们把所有`hello`逻辑移到一个插件. 想要创建一个插件, 我们只需要实现插件({{rust_type(type="trait" name="Plugin" crate="bevy_app" no_mod=true)}})接口. 将一下代码添加到`main.rs`文件中:

```rs
pub struct HelloPlugin;

impl Plugin for HelloPlugin {
    fn build(&self, app: &mut AppBuilder) {
        // add things to your app here
    }
}
```

然后像这样在App中注册插件:
```rs
fn main() {
    App::build()
        .add_plugins(DefaultPlugins)
        .add_plugin(HelloPlugin)
        .add_startup_system(add_people.system())
        .add_system(hello_world.system())
        .add_system(greet_people.system())
        .run();
}
```

现在只剩下我们把我们的`系统`移到`HelloPlugin`中, 这也只是一个复制粘贴的过程. 在我们插件`build()`函数中的变量`app`, 和我们在`main()`函数中使用的构造器类型相同:

```rs
impl Plugin for HelloPlugin {
    fn build(&self, app: &mut AppBuilder) {
        app.add_startup_system(add_people.system())
            .add_system(hello_world.system())
            .add_system(greet_people.system());
    }
}

fn main() {
    App::build()
        .add_plugins(DefaultPlugins)
        .add_plugin(HelloPlugin)
        .run();
}
```

再尝试运行一次应用. 它能准确的像之前一样工作. 在下一章节中, 我们将用`Resources`的概念来解决`hello`垃圾信息的问题.
