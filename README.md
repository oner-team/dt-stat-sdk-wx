# `dt-sdk-wx` 小程序埋点SDK

## 1. 引入sdk

把`stat.min.js`和`stat_config.js`这两个文件放在小程序的 utils 目录下，然后在 app.js 第一行添加以下代码

```js
const stat = require('./utils/stat.min.js')
stat.init()
```

现在在其他Page里就可以通过getApp方法来获取stat的`track`方法了

```js
const app = getApp()
app.stat.track(eventCode, params)
```


## 2. stat_config.js参数配置

- name： SDK使用的一个默认的全局变量，会注册在App全局函数内，在Page中可以通过app[name]来访问使用，默认值是stat。

- server_url： 数据接收地址。**注意**: 请在微信开发设置，服务器域名的 request 合法域名内，把这个地址加上

- show_log： 设置true，会在控制台打logger，显示发送的数据。设置false表示不显示。默认为true

- getLocation: 设置为true时，会在用户打开小程序时申请授权获取用户位置信息。默认为false


## 3. Page中追踪自定义事件

#### 3.1 发送事件 

`.track(eventName, eventValue)`

通过`track`方式可以将埋点数据发送给后端，第一个参数为事件名，第二个参数为事件传回参数，要求为**对象**。对象内的值要求为数值、字符串、布尔值、日期或数组等类型。


```js
const app = getApp()
app.stat.track('placeOrder', {
	ProductId: '1000',
	ProductName: 'Macbook pro',
	ProductPrice: '14000',
	ProductCatalog: '电脑',
})
```

#### 3.2 添加公用参数

`.registerParam(param)`

可以在小程序页面Page()执行前使用`registerParam`方法来注册事件公共参数，这样在后续的所有自定义事件中都会添加返回这些公共参数。

```js
const app = getApp()
app.stat.registerParam({
	userId: 123,
	userName: 'Super Mario',
})
```

## 4. 预置事件

#### 4.1 预置事件追踪

为了更方便准确的追踪小程序在各个生命周期的状态变化，我们的SDK提供了预置事件追踪功能。

| 预置事件名称   | 相应小程序生命周期函数     | 触发时机           | 说明  |
| ------------- |:------ |:-------------:| -----:|
| $WXMPLaunch   | App.onLaunch  | 小程序重新打开时会触发 | 只会在小程序初始化完成时触发一次 |
| $WXMPShow     | App.onShow | 小程序启动或从后台进入前台时触发 | 启动小程序时 |
| $WXMPHide     | App.onHide | 点击小程序右上角退出按钮、微信进入后台、进入小程序关于页面、手机锁屏、小程序进程被杀死时触发 | 小程序从前台进入后台 |
| $WXMPViewScreen | Page.onShow | 小程序启动打开页面、从后台进入前台打开页面时触发 | 每次打开页面都会触发 |

可以在config.js中添加以下参数，开启预置事件自动采集。

```js
autoTrack: {
  appLaunch: true, //是否采集 $WXMPLaunch 事件，true 代表开启。
  appShow: true, //是否采集 $WXMPShow 事件，true 代表开启。
  appHide: true, //是否采集 $WXMPHide 事件，true 代表开启。
  pageShow: true, //是否采集 $WXMPViewScreen 事件，true 代表开启。
}
```

#### 4.2 预置事件添加自定义属性

在上一节我们可以设置是否启用追踪预置事件，用户也可以在预置事件`$WXMPLaunch`、`$WXMPShow`、`$WXMPHide`和`$WXMPViewScreen`几个事件中添加自定义属性。

可以通过`getApp().sensors.para.autoTrack[param] = value`添加自定义属性。其中`param`为"appLaunch"、"appShow"、"appHide"、`pageShow`等值中的一个，`value`为对象或**返回值是对象**的函数。

例如：

```js
const stat = requre('./utils/dt-sdk.min.js')
App({
  onLaunch: data => {
  	stat.para.autoTrack.appLaunch = {
  	  userName: '王二',
  	  age: 16,
  	  gender: 'male',
  	}
  }
})
```

也可以通过返回对象的函数的方式来实现

```js
const stat = requre('./utils/dt-sdk.min.js')
App({
  onLaunch: data => {
  	stat.para.autoTrack.appLaunch = () => ({
  	  userName: '王二',
  	  age: 16,
  	  gender: 'male',
  	})
  }
})

```

$brand: "devtools",
    $country: '',
    $province: '',
    $city: '',
    $nn: '', // nickName
    $gd: '', // gender
    $au: '', // avatarUrl

## 5. 预置属性

| 预置字段名称   | 类型     | 说明          |
| ------------- |:------ |:-------------:|
| $event_name   | String  | 事件名称 |
| $project_id   | String  | 项目ID |
| $lib   | String  | SDK类型 |
| $libVersion | String | SDK版本 |
| $screenHeight  | Number | 小程序窗口高度 |
| $screenWidth | Number | 小程序窗口宽度 |
| $brand| String | 手机品牌 |
| $nn| String | 微信昵称 |
| $gd| String | 性别， 0：未知 1：男 2：女 |
| $au| String | 用户头像链接 |
| $model| String | 设备型号 |
| $networkType| String| 网络类型 |
| $os | String  |  操作系统  |
| $osVersion | String  |  操作系统版本  |
| $wxVersion | String  |  微信版本  |
| $urlPath | String  |  页面路径   |
| $isFirstDay | Bool | 是否首日访问   |
| $latestScene | String   | 最近一次启动场景值   |
| $isFirstTime | Bool  | 是否是首次访问，绘制appLaunch状态时触发返回   |
| $scene | String  |  启动场景，会在appLaunch时触发返回  |
| $eventDuration | Number  | 时长，触发appHide状态时返回   |
| $latitude | Number  | 纬度，配置项中getLocation配置为true时返回   |
| $longitude | Number  | 经度，配置项中getLocation配置为true时返回   |
| $accuracy | Number  | 精确度，配置项中getLocation配置为true时返回   |
| $speed | Number  | 速度，配置项中getLocation配置为true时返回   |
| $country| String | 用户所属国家 |
| $province| String | 用户所属省份 |
| $city| String | 用户所属城市 |
| $isDefaultTrack | Bool  | 预置事件会返回该字段，返回值为true   |
| $distinct_id| String | 插件分配的唯一ID |
| $type| String | 日志触发类型 |

## 6. 具体操作流程

首先先将dt-stat-wx.min.js和config.js放到utils文件夹下

#### 6.1 在根目录下的app.js中加入

```js
var stat = require('./utils/dt-stat-wx.min.js')

App({
	onLaunch: () => {
		stat.track('launch', {name: '赵四', age: 66})
	}
})

```

#### 6.2 在Pages文件夹里的js可以通过`getApp()`来获取sdk实例

```js
const app = getApp()

Page({
	onLoad: () => {
		app.stat.track('loadPage', {descr: '加载购买页', id: 66666})
	}
})
```

#### 6.3 获取预置属性

使用`getPresetParam`可以获取到SDK默认预置的属性值。

```js
stat.getPresetParam()
```