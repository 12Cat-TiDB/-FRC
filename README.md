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
让所有人都能向 tiup 贡献组件，打造一个组件市场的雏形

## 背景 & 动机
TiUP 作为包管理工具，目前只支持配置一个镜像，该镜像主要用于发布 TiDB 数据的内核组件和部署组件。TiUP 也是有野心的，想要管理和发布更多的内容，不想局限一个镜像，也不想仅仅局限于 TiDB组件。

场景1 ：包管理平台，提升开发效率
开发团队需要一个平台管理和分发开发工具以及最新的产品daily build 包，团队可以快速搭建工具镜像和产品镜像。开发者可以方便的从内网镜像找到相关工具并一键安装或升级，比从外网下载更加快速，提升开发效率。

场景2 ：TiDB 组件的发布平台
社区开发者做了一个 TiDB 的组件，可以给 TiDB 快速导入数据并提供一个 demo 应用。这个组件可以让更多人快速了解 TiDB 的使用，但是由于 TiDB 没有组件市场或者发布平台，这个组件没有合适的发布渠道，并不能被真正需要的人获取。12Cat 项目可以提供一个 TiDB 组件的发布平台，让社区开发将自己的组件上传到平台上。 TiUP 的使用者只需要配置了组件平台的镜像，就可以 list 发现各种组件，选择自己想要的进行使用。

场景3: 终于不用被切了镜像而找不到组件而困扰
由于 TiUP 只支持设置一个镜像，官方镜像又不能添加私有组件，很多开发者不得不在私有镜像和官方镜像之间切换，经常因为切了镜像而找不到组件。12Cat 项目会让 TiUP支持同时设置多个镜像，彻底解决切镜像的苦恼。

## 项目设计
本项目主要有三个大的功能点：
- 让 TiUP 支持同时设置多个镜像，并支持快速搭建新镜像。
- 让 TiUP 支持各种工具和组件的安装和升级
- 提供一个可以让社区开发者贡献组件的开发者镜像，打造 TiDB 组件市场

## 缺点

## FAQ

## 效果验证
### 环境
### Config
### Workload
### 结果
