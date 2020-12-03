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
fn greet_people(time: Res<Time>, _person: &Person, name: &Name) {
    println!("hello {}!", name.0);
}
```

`Res`和`ResMut`指针分别提供对资源的读写. 请注意, 资源 **必须** 在组件之前, 否则你的函数将不能转换为系统. 实际上, 我建议你尝试把`time: Res<Time>`放置在无效的位置上, 这样你就可以知道`无效系统`编译错误是什么样子了.

`Time`的`delta_seconds`字段给我们提供了上一次更新以来所经过的时间. 但是为了每2秒钟运行一次系统(system), 我们必须跟踪经过的一系列更新的时间. 为了简化过程, Bevy提供了`Timer`类型. 让我们创建一个新的资源(`Timer`), 来追踪经过的时间.

```rs
struct GreetTimer(Timer);

fn greet_people(
    time: Res<Time>, mut timer: ResMut<GreetTimer>, _person: &Person, name: &Name) {
    // update our timer with the time elapsed since the last update
    timer.0.tick(time.delta_seconds);

    // check to see if the timer has finished. if it has, print our message
    if timer.0.finished {
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

现在, `cargo run`运行一下, 他看上像那么回事, 但是不完全是!

### The Bug

好消息是我们没有了控制台的垃圾信息. 但是似乎我们在随机打印名字, 时间也不对. 这发生了什么?

问题是该系统在每一个拥有`Person`和`Name`组件的实体上运行一次. 我们有3个实体满足这个条件, 所以在每一次刷新的时候, 我们刷新了3次计时器. 这有就意味着当一个计时器结束, 第一个要刷新的实体将重置`GreetTimer`, 而其他实体都不会打印他们的信息. 这意味着其他2个人没有机会打招呼. 这太没礼貌了!

我们需要一种方式让我们的系统(System)在每一次刷新的时候运行一次. 但不是计时器(`Timer`)结束的时候和每一个人打招呼. 幸运的是Bevy ECS为此提供了"查询"(`Queries`)

让我们继续下一章, 以便我们解决这个讨厌的Bug!
