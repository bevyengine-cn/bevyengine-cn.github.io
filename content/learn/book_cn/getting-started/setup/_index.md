+++
title = "设置"
weight = 1
sort_by = "weight"
template = "book-section.html"
page_template = "book-section.html"
+++

我知道你渴望开始制作游戏, 但我们需要先做少量的设置.

## Rust 设置

所有Bevy应用和引擎代码都是用Rust编写的. 这意味着在我们开始之前, 我们需要搭建我们的Rust开发环境.

### 安装 Rust

按照[Rust入门指南]((https://www.rust-lang.org/learn/get-started))安装Rust

一旦完成, ```rustc```编译器和```cargo```构建系统应该成功安装在你的可执行路径中.

### 安装系统依赖
* [Linux](https://github.com/bevyengine/bevy/blob/master/docs/linux_dependencies.md)
* Windows: 你可以很好的进行下去
* MacOS: 没有要安装的依赖

### 代码编辑器 / IDE

你可以使用任何你喜欢编辑器. 但我们强烈推荐拥有[Rust Analyzer](https://github.com/rust-analyzer/rust-analyzer)插件的编辑器. `Rust Analyzer`仍在开发中, 但它已经提供了顶级的自动补全和代码提示. [Visual Studio Code](https://code.visualstudio.com/)拥有官方支持的[Rust Analyzer插件](https://marketplace.visualstudio.com/items?itemName=matklad.rust-analyzer).

### Rust 学习资料

本书的目的是学习Bevy, 所以不会提供完整的Rust教学. 如果你想学习更多关于Rust语言的内容, 请查看以下资源:

* [**The Rust Book**](https://doc.rust-lang.org/book/): the best place to learn Rust from scratch
* [**Rust by Example**](https://doc.rust-lang.org/rust-by-example/): learn Rust by working through live coding examples


## 新建一个Bevy项目

现在我们已经准备好新建一个Bevy项目了! Bevy只是正常的Rust依赖项. 你可以将其添加到现有的Rust项目中, 也可以新建一个. 为了完整起见, 我们从零开始.

### 新建一个可执行的Rust项目

首先, 导航到要在其中创建新项目的文件夹. 然后, 运行以下命令以创建一个包含rust可执行项目的新文件夹：

```
cargo new my_bevy_game
cd my_bevy_game
```

现在执行```cargo run```来编译和运行你的项目. 你应该可以看到```Hello, world!```打印在命令行中. 用你选择的编辑器中打开```my_bevy_game```文件夹, 花一些时间来浏览这些文件.

```main.rs``` 是整个程序的入口:
```rs
fn main() {
    println!("Hello, world!");
}
```

```Cargo.toml``` 是你的项目文件. 它包含了诸如名字, 依赖和构建配置等元数据.

```toml
[package]
name = "my_bevy_game"
version = "0.1.0"
authors = ["You <you@veryrealemail.com>"]
edition = "2018"

[dependencies]
```

### Enable Fast Compiles (Optional)

Bevy can be built just fine using default configuration on stable Rust. However for really fast iterative compiles, we recommend the following configuration:

* **LLD linker**: The Rust compiler spends a lot of time in the "link" step. LLD is _much faster_ at linking than the default Rust linker. To install LLD, find your OS below and run the given command:
    * **Ubuntu**: `sudo apt-get install lld`
    * **Arch**: `sudo pacman -S lld`
    * **Windows**: Ensure you have the latest [cargo-binutils](https://github.com/rust-embedded/cargo-binutils)
        ```
        cargo install -f cargo-binutils
        rustup component add llvm-tools-preview
        ```
    * **MacOS**: Modern LLD does not yet support MacOS, but we can use zld instead: `brew install michaeleisel/zld/zld`
* **Nightly Rust Compiler**: This gives access to the latest performance improvements and "unstable" optimizations
    ```
    # Install the nightly toolchain
    rustup toolchain install nightly
    # Configure your current project to use nightly (run this command within the project)
    rustup override set nightly
    # OR configure cargo to use nightly for all projects -- switch back with `rustup default stable`
    rustup default nightly
    ```
    * You can use `cargo +nightly ...` if you don't want to change the default to nightly.
* **Generic Sharing**: Allows crates to share monomorphized generic code instead of duplicating it. In some cases this allows us to "precompile" generic code so it doesn't affect iterative compiles. This is only available on nightly Rust.

To enable fast compiles, install the nightly rust compiler and LLD. Then copy [this file](https://github.com/bevyengine/bevy/blob/master/.cargo/config_fast_builds) to `YOUR_WORKSPACE/.cargo/config.toml`. For the project in this guide, that would be `my_bevy_game/.cargo/config.toml`.

### 将Bevy添加到项目的Cargo.toml

Bevy可以作为一个库在[crates.io](https://crates.io/crates/bevy)中获取. 它是Rust的官方软件包库. 找到最新的版本号([![Crates.io](https://img.shields.io/crates/v/bevy.svg)](https://crates.io/crates/bevy))并把它加到Cargo.toml文件中:

```toml
[package]
name = "my_bevy_game"
version = "0.1.0"
authors = ["You <you@veryrealemail.com>"]
edition = "2018"

[dependencies]
bevy = "0.3" # make sure this is the latest version
```

再次执行```cargo run```. Bevy依赖项开始构建. 这需要一点时间, 因为你实际上是从头构建引擎. 你只需要进行一次完整的构建. 以后每次都会很快的!

如果出现问题, 请查看[故障排除部分](/learn/book_cn/troubleshooting/) or [在Discord中寻求帮助](https://discord.com/invite/gMUk5Ph).

现在我们已经建立了Bevy项目，我们准备开始制作我们的第一个Bevy应用程序！
