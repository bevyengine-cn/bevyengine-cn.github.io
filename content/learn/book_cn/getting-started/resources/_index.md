+++
title = "资源"
weight = 5
sort_by = "weight"
template = "book-section.html"
page_template = "book-section.html"
+++

**实体(Entities)** 和 **组件(Components)** 非常合适表示复杂的, 可查询的数据组. 但是大多数应用还是需要某种`全局唯一`数据. 在Bevy ECS中, 我们使用 **资源(Resources)** 来表示全局唯一数据.

这儿有一些可以被当做**Resources**的数据例子:  
* 时钟
* 资源集合 (声音, 纹理, 网格)
* 渲染器

## 用Resources来追踪时间

让我们通过每2秒钟打印一次`hello`来解决应用的垃圾信息问题. 我们将通过{{rust_type(type="struct" crate="bevy_core" name="Time")}}资源来完成它. 该资源通过`add_plugins(DefaultPlugins)`自动添加到我们的应用中.

简单点, 从应用中删除`hello_world`系统. 这样我们只需要适配`greet_people`系统.

访问资源的方式与访问组件的方式基本相同. 你可以在你的系统中像这样访问`Time`资源:

```rs
fn greet_people(time: Res<Time>, query: Query<&Name, With<Person>>) {
    for name in query.iter() {
        println!("hello {}!", name.0);
    }
}
```

`Res`和`ResMut`指针分别提供对资源的读写.

`Time`的`delta_seconds`字段给我们提供了上一次更新以来所经过的时间. 但是为了每2秒钟运行一次系统(system), 我们必须跟踪经过的一系列更新的时间. 为了简化过程, Bevy提供了`Timer`类型. 让我们创建一个新的资源(`Timer`), 来追踪经过的时间.

```rs
struct GreetTimer(Timer);

fn greet_people(
    time: Res<Time>, mut timer: ResMut<GreetTimer>, query: Query<&Name, With<Person>>) {
    // update our timer with the time elapsed since the last update
    // if the timer hasn't finished yet, we return
    if !timer.0.tick(time.delta_seconds()).just_finished() {
        return;
    }

    for name in query.iter() {
        println!("hello {}!", name.0);
    }
}
```

现在剩下的就是把`GreetTimer`这个资源添加到我们的`HelloPlugin`中:
```rs
impl Plugin for HelloPlugin {
    fn build(&self, app: &mut AppBuilder) {
        // the reason we call from_seconds with the true flag is to make the timer repeat itself
        app.add_resource(GreetTimer(Timer::from_seconds(2.0, true)))
            .add_startup_system(add_people.system())
            .add_system(greet_people.system());
    }
}
```

现在, `cargo run`这个App, 它将以比较合理的方式向人们打招呼.
