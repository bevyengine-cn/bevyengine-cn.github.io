# Bevy Website

The source files for https://bevyengine.org. This includes official Bevy news and docs, so if you would like to contribute feel free to create a pull request!

## Zola

The Bevy website is built using the Zola static site engine. In our experience, it is fast, flexible, and straightforward to use. [Check it out here](https://www.getzola.org/)!


# 中文

Bevy的官网是用`Zola`构建的. [详情](https://www.getzola.org/)!

## 如何翻译官网文档.

- [安装Zola](https://www.getzola.org/documentation/getting-started/installation/)  
- 推荐源码安装.
```bash
git clone https://github.com/getzola/zola.git
cd zola
cargo build --release
```  
- 然后把路径加到`$PATH`里. 或者把`target/release`中的`zola.*`复制到`~/.cargo/bin`  
- 然后用`zola`在本地编译并运行:
```bash
zola serve
```
