+++
title = "查询"
weight = 6
sort_by = "weight"
template = "book-section.html"
page_template = "book-section.html"
+++

在Bevy ECS中, 查询(`Queries`)可以让你直接控制实体迭代. 它们还提供了一些额外的过滤选项, 例如`With<Component>`和`Without<Component>`过滤器.

## 解决我们之前的Bug.

到目前为止, 我们一直在使用 **For-Each 系统**, 该系统直接引用组件, 并在每个实体上运行一次. 就我们的`greet_people`系统而言, **For-Each 系统**的行为与我们想要的逻辑不太一致, 因为它对每个实体都运行一次:

```rs
fn greet_people(
    time: Res<Time>, mut timer: ResMut<GreetTimer>, _person: &Person, name: &Name) {
    timer.0.tick(time.delta_seconds);
    if timer.0.finished {
        println!("hello {}!", name.0);
    }
}
```

解决方案是用 **查询(Query)系统** 取代:

```rs
fn greet_people(
    time: Res<Time>, mut timer: ResMut<GreetTimer>, query: Query<(&Person, &Name)>) {
    timer.0.tick(time.delta_seconds);
    if timer.0.finished {
        for (_person, name) in query.iter() {
            println!("hello {}!", name.0);
        }
    }
}
```

现在我们的应用应该可以每2秒钟向所有`People`打招呼了!

## Query Systems vs For-Each Systems

现在我们有两种不同的系统类型, 你如何选择?

这很大程度上是一个美学问题. 一些开发者喜欢简单和易读的 **For-Each Systems**, 并会尽可能的使用它. 其他人则喜欢灵活的 **Query Systems**. 这里没有正确答案. 你应该使用对你最有效的方法!
