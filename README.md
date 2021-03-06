# 一、介绍

`mpvue-vant`记录了我们团队开发中在`mpvue`中使用`Vant Weapp`组件库所踩下的坑，在这里分享给大家，让`mpvue`开发者可以使用`vant`组件库进行开发，避免踩不必要的坑。

此教程是在[dov-yih](https://github.com/dov-yih)一同协助下完成。经过测试，[Vant Weapp](https://youzan.github.io/vant-weapp/#/intro)下所有组件都能够在`mpvue`中使用

- [demo 地址](./demo)
- [更新日志](./blog/update.md)

# 二、使用方法

## 克隆仓库

> 注意：由于一些 BUG 我修改了源代码，所以请克隆本仓库代码。

```
git clone https://github.com/xxxsimons/mpvue-vant.git
```

**将仓库中的 vant 文件夹复制到你的项目目录 static 下**

## 配置 webpack 为 vant 支持 ES6

> 请记得打开微信开发者工具中的 ES6 转 ES5 功能

```
// build/webpack.base.conf.js 的babel-loader中
      {
        test: /\.js$/,
        include: [resolve('src'), resolve('test'), resolve('static/vant')], // 添加vant文件目录
        use: [
          'babel-loader',
          {
            loader: 'mpvue-loader',
            options: {
              checkMPEntry: true
            }
          },
        ]
      },
```

## 配置 eslint，忽略 vant 目录检查

```
// 根目录.eslintignore文件

static/vant/*/*.js //添加这一行
```

## 引入

在需要引入的页面目录下的`main.json`文件中

```
{
  "usingComponents": {
    "van-button": "/static/vant/button/index",
  }
}
```

## 使用

```
<van-button>测试</van-button>
```

# 三、注意事项

具体组件 api 文档参考[Vant Weapp](https://youzan.github.io/vant-weapp/#/intro)

## 1. 使用方式

mpvue 和原生小程序的方式有所不同。

### 1.1 数据绑定

原生小程序使用方式为

```
value="{{value}}"
```

mpvue 使用方式

```
v-bind:value="value"
//或者
:value="value"
```

### 1.2 事件监听

原生小程序使用方式

```
bind:click="onClick"
```

mpvue 使用方式

```
@click="onClick"
```

### 1.3 vue 中组件引入

`vant`中像`notify`这种操作反馈类的组件都有两个引入，一是组件的引入，这个在`main.json`中引入；另一个是方法的引入，需要在`vue`文件中`import`引入，值得注意的是，这里的引入不能使用绝对路径，可以用类似于这样的相对路径。

```
import Notify from '@/../static/notify/notify' //@是mpvue的一个别名，指向src目录
```

### 1.4 获取 event

值得注意的是，`mpvue`中获取`event`值与原生小程序有所不同。举例：

```
onChange(event){ // 获取表单组件filed的值
  console.log(event.mp.detail) // 注意加入mp
}
```

## 2. BUG 及处理方法

### 2.1 监听名

mpvue 里面无法使用`@click-icon`这样的监听名,因此如果 API 文档里面有出现这样的监听名，那么需要手动修改源代码。

可以改成驼峰式的监听名。

eg: 我在`field`组件中就遇到这个问题，我的做法是：

```
// static/vant/field/index.js

this.$emit('click-icon');

// 修改为:

this.$emit('clickIcon');
```

### 2.2 报错

我在迁移过程中遇到如下报错信息

```
Cannot assign to read only property 'exports' of object '#<Object>' (mix require and export
```

这是`module.export`和`import`混用的原因导致的。解决办法就是修改`module.export`为`export`

eg:

```
// static/vant/utils/index.js 10行

modules.export = {
  isObj,
  isDef
}

// 修改为

export {
  isObj,
  isDef
}
```

> 最新的`vant weapp`组件库已经修复了`export`这个问题。 大家也可以直接拉去`vant weapp`的最新代码了，目前暂时只发现了有上面说的监听名的 BUG，大家用我给的解决方案修改一下源代码就可以了。同时本仓库也会同步更新最新的`vant weapp`组件库。

# 3. 其他组件库

目前比较好的组件库有三个，[Wux Weapp](https://wux-weapp.github.io/wux-weapp/#/),[iview weapp](https://weapp.iviewui.com/),[Vant Weapp](https://youzan.github.io/vant-weapp/#/intro)。

这三者都是用原生小程序写的组件库，因此理论上来说，在 mpvue 中都是可以无缝使用的。不同组件库的组件都不一样，有的更丰富，有的逻辑更完善，有的文档更清晰。因此用什么组件，还需要自己取舍。

比如：三者中，唯有[Wux Weapp](https://wux-weapp.github.io/wux-weapp/#/)有日历组件，而且它里面还有一些更高级的组件可以使用。

使用方法上，几乎没有差异。值得注意的是，大家复制源代码到自己项目上时，应该复制`/dist/`目录下的文件。因为这里是经过编译后的。

如果大家使用过程中遇到什么 BUG，可以通过提[issues](https://github.com/xxxsimons/mpvue-vant/issues)的方式让我知道，大家一起踩坑吧！
