# 12Cat
# 基本介绍
## 团队名称：12只喵
- [qqqdan](https://github.com/qqqdan): 产品喵，喵2只
- [nexustar](https://github.com/nexustar): 后端研发喵，没有喵
- [srstack](https://github.com/srstack): 后端研发喵，喵8只
- [AstroProfundis](https://github.com/AstroProfundis): 后端研发喵，喵2只

## 项目进展
TODO
## 项目介绍
使 TiUP 迈向通用包管理工具

## 背景 & 动机
TiUP 作为包管理工具，目前只支持配置一个镜像，该镜像主要用于发布 TiDB 数据的内核组件和部署组件。TiUP 也是有野心的，想要管理和发布更多的内容，不想局限一个镜像，也不想仅仅局限于 TiDB组件。

场景1 ：包管理平台，提升开发效率

开发团队需要一个平台管理和分发开发工具以及最新的产品daily build 包，团队可以快速搭建工具镜像和产品镜像。开发者可以方便的从内网镜像找到相关工具并一键安装或升级，比从外网下载更加快速，提升开发效率。

场景2 ：TiDB 组件的发布平台

社区开发者做了一个 TiDB 的组件，可以给 TiDB 快速导入数据并提供一个 demo 应用。这个组件可以让更多人快速了解 TiDB 的使用，但是由于 TiDB 没有组件市场或者发布平台，这个组件没有合适的发布渠道，并不能被真正需要的人获取。12Cat 项目可以提供一个 TiDB 组件的发布平台，让社区开发将自己的组件上传到平台上。 TiUP 的使用者只需要配置了组件平台的镜像，就可以 list 发现各种组件，选择自己想要的进行使用。

场景3: 终于不用被切了镜像而找不到组件而困扰

由于 TiUP 只支持设置一个镜像，官方镜像又不能添加私有组件，很多开发者不得不在私有镜像和官方镜像之间切换，经常因为切了镜像而找不到组件。12Cat 项目会让 TiUP支持同时设置多个镜像，彻底解决切镜像的苦恼。

## 现状分析

tiup 定位于 TiDB 生态的包管理工具，能够从 PingCAP 官方软件仓库下载 TiDB 组件和周边工具。但是从能力上来说，TiUP 可以做到的远不止于此。它是以一个类似“绿色软件”的方式来实现的、无需 root 权限的、支持多版本共存和快速切换的通用的包管理器。如果通过现有的能力，可以反向将其的产品定义变得更加通用。

对比其他包管理工具，tiup 有一些独特且值得保留的特点。
- 对比 apt/dnf/pacman：跨系统且不需要 root 权限
- 对比 homebrew：专注二进制程序分发而不是源码编译、无需 root 权限
- 对比 nix：使用方便
- 对比 flatpak：跨系统
- 对比其他所有：可以用 tiup xxx:v1.x.x 的方式快速在不同版本间切换使用

## hackathon 目标

1. 使 TiUP 支持同时使用多个软件仓库 @nexustar @AstroProfundis
2. 利用 github pages 或其他服务来搭建一个 “tiup community”软件仓库 @srstack
3. 向“tiup community”软件仓库中添加开发者常用命令行工具 @srstack
4. maybe 让 tiup-playground 和 tiup-cluster也支持多软件仓库 @nexustar @AstroProfundis
5. 增加 tiup 的用法，更方便管理和使用多版本软件 @nexustar

## 使用设计

保留原来的总是使用 tiup 前缀的使用方式，也添加像其他包管理工具一样将组件路径添加到 PATH 的用法

1. tiup 传统用法
```
tiup zstd:v1.2.3
```

2. 新用法
```
tiup install zstd
zstd xxxxxx
tiup unlink zstd
tiup link zstd:v1.2.3
```

## 项目设计
本项目主要有三个大的功能点：
- 让 TiUP 支持同时设置多个镜像，并支持快速搭建新镜像。
- 让 TiUP 支持各种工具和组件的安装和升级
- 提供一个可以让社区开发者贡献组件的开发者镜像，打造 TiDB 组件市场

### 多镜像设计
![设计图](/media/pic.png)

<<<<<<< HEAD
#### 兼容性
规则：考虑兼容性，如果用户只有一个镜像，mirror相关的操作与老版本一致，不变化。

Check list：
- [ ] 通过命令 tiup mirror init 从零生成
- [ ] 通过命令 tiup mirror clone 默认从已有全部镜像克隆
- [ ] tiup mirror set  不再使用，提示使用新的命令
 
#### 配置多个mirror
规则：
- Mirror有权重、别名、path
- Mirror的权重默认是100，用户可以修改，越小优先级越高。权重相同，按别名字母顺序。
- Mirror的权重和path支持命令修改。别名也可以改。
- 添加mirror时，权重默认100，必须提供别名，别名不允许重名。

命令：
- tiup mirror add <name> <url> --priority=11
- tiup mirror delete <name> 
- tiup mirror set <name> url=XXX  or priority =XXX
- tiup mirror rename <name> <string>
- tiup mirror show ： 显示路径和order
```
    Priority    name    		path
    1	  AAA 			https://tiup-mirrors.pingcap.com
    100	 BBB 			XXXXXXXXXXX
 ```

#### 使用多mirror：多个mirror的处理顺序
- Install，list，status等操作的时候，都会按顺序去每个mirror获取数据
- 如果多个mirror有相同组件的包，用优先级高的mirror

提示信息：
- 每次通过install，list，status逐个查询多个mirror时以及添加镜像时，发现用户配置了超过3个镜像，提示 "You have configed more than 3 tiup mirrors, and this may make tiup response slower."
- 当有一个或多个mirror失联时，提示失联的mirror信息

#### Mirror失联的处理

- 任意mirror失联时就停止工作，直接报错。
- 提供忽略某个异常 mirror 的参数，类似 yum 的 `--disablerepo`

备注：直接失败可能不太友好，但是规避一些异常场景，第一版本以这种方式发布，如果客户使用有问题，可以讨论改进。


#### 非官方Mirror的软件包发布
- tiup mirror publish <mirrorname> <comp-name> <version> <tarball> <entry>  [flags]
  - 增加mirrorname的配置: <targetmirror> 必填 ，填mirror别名
- tiup mirror modify 同上处理 
- tiup mirror clone 不需要加参数，强制使用mirror全集
- tiup mirror grant 增加mirrorname配置
- tiup mirror rotate 增加mirrorname配置

#### 增强软件包自描述能力
- 在软件包中增加 meta 文件描述 entrypoint 等关键信息
- 增加安装后 hook 执行入口



## 缺点

## FAQ

## 效果验证
### 环境
### Config
### Workload
### 结果
