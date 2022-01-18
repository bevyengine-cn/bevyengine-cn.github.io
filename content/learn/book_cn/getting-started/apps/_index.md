+++
title = "Apps"
weight = 2
sort_by = "weight"
template = "book-section.html"
page_template = "book-section.html"
+++

Bevy程序看起来像{{rust_type(type="struct", crate="bevy_app", name="App", no_mod=true, plural=true)}}. 简单的Bevy应用看起来像这样:

```rs
use bevy::prelude::*;

fn main() {
    App::new().run();
}
```

简单漂亮吧? 复制代码到你的```main.rs```文件中, 然后执行:

```
cargo run
```

在您的项目文件夹中. 您会发现...什么也没发生. 这是因为我们尚未告诉我们的应用任何操作! 应用程序只是空壳, 能够运行我们的应用程序逻辑. 让我们使用Bevy ECS向我们的应用程序添加一些逻辑. 
