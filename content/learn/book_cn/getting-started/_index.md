+++
title = "入门指南"
weight = 2
sort_by = "weight"
template = "book-section.html"
page_template = "book-section.html"
+++

这一章将尽可能快的让你开始Bevy的旅程. 将带你配置开发环境并写一个简单的Bevy应用.

## 快速开始

如果你想先自我体验, 并且已经配置好了Rust的环境. 或者感觉这个"快速开始"很简单. 请移步下一页.

注意: "fast compiles"(快速编译)的设置在下一页, 看不懂的就先去看下一页.

### 运行Bevy的例子.

1. Clone the [Bevy repo](https://github.com/bevyengine/bevy):
    ```
    git clone https://github.com/bevyengine/bevy
    ```
2. Navigate to the new "bevy" folder
    ```
    cd bevy
    ```
3. Try the examples in the [examples folder](https://github.com/bevyengine/bevy/tree/master/examples)
    ```
    cargo run --example breakout
    ```

### 把Bevy作为依赖的库.
 
Bevy可以作为一个库在[crates.io](https://crates.io/crates/bevy)中获取.

像下面这样, 在你项目的Cargo.toml文件中, 添加Bevy库:

```toml
[dependencies]
bevy = "0.4" # make sure this is the latest version
```

目前`Bevy`库的版本: 

<a href="https://crates.io/crates/bevy"><img src="https://img.shields.io/crates/v/bevy.svg" style="height: 1.7rem;"/></a>

**_注意:_** Bevy当前正在快速更新. 如果依赖于git仓库, 能比`cargo crate`更快的获取到最新更新. *然而*, **经常的迭代会对APIs和代码造成影响**. 所以, 了解Bevy的最新进展很重要. <span class="warning">**_我们不推荐新手使用git的方式来配置依赖_**</span>
```toml
[dependencies]
bevy = { git = "https://github.com/bevyengine/bevy" }
```

一般来说, 锁定特殊的`commit hash`(提交哈希值)是一个好办法, 这样你就能控制何时更新. 你可以在[latest commit hash here](https://github.com/bevyengine/bevy/commits/master)中查找(每个提交的右边).
```toml
[dependencies]
bevy = { git = "https://github.com/bevyengine/bevy", rev = "25f62f7250a0d750068dc32533b9433f7985af98" }
```
