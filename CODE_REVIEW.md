# 微信小程序前端代码审查清单

## 代码静态检查工具

### 使用 eslint 工具对 javascript 代码进行检查

`eslint` 检查的规范继承自` eslint-config-standard` 检验规则，具体的规则介绍参照链接：[cn.eslint.org/docs/rules/](https://cn.eslint.org/docs/rules/) ，这里及以下部分不再重复介绍这些检验规则。

## 代码规范

### JS 采用 Camel Case 小驼峰式命名

推荐：

```javascript
const studioInfo = {}
```

### CSS 类名采用 BEM 命名规范

推荐：

```css
.studio-name
```

### 命名符合语义化

命名需要符合语义化，如果函数命名，可以采用加上动词前缀：

| 动词 | 含义                   |
| ---- | ---------------------- |
| can  | 判断是否可执行某个动作 |
| has  | 判断是否含有某个值     |
| is   | 判断是否为某个值       |
| get  | 获取某个值             |
| set  | 设置某个值             |

### 常量需要命名

推荐：

```javascript
const MAX_NUMBER = 10
let row = Math.ceil(num/COL_NUM)
```

不推荐：

```javascript
let row = Math.ceil(num/10)
```

## typescript规范

### interface、类型命名首字母大写

推荐：

```typescript
interface StudioInfo {
	name:string
}
```

### 尽量避免使用any

不推荐:

```typescript
const studioInfo:any = {}
```

## 代码思路

### 重复代码优先考虑封装组件

对于当前已有的重复代码片段，以及常用场景（例如消息通知，弹窗）等的代码需要考虑封装为一个组件。

### 组件优先考虑无状态化

封装组件时，大多数时候都推荐将组件中使用到的属性通过properties由父级外传递进组件中使用。

### 避免循环引用问题

应当避免组件或工具类循环引用的问题，例如：

```typescript
// env.ts文件
import { timeResetter } from './utils'
export class EnvHelper {
	let time = timeResetter()
	// ......
}

// utils.ts文件
import { EnvHelper } from './env'
export const timeResetter = () => {
	const env = new EnvHelper()
  // ......
}
```

### 条件判断

不满足条件的情况要处理或加注释，不推荐留空，例如：

```typescript
import { studioInfo } from './api'
const currentTime = new Date()
if(currentTime > studioInfo.time) {
	// ......
} else {
	// ......
}
```

## 样式

### 保证对设计稿的还原度

优先考虑保证对设计稿的还原度，避免遗漏元素、间距、线条等。

### 屏幕适配

保证在大部分屏幕尺寸下的视觉统一性与可用性。

保证在极端屏幕尺寸下的可用性。

### 复用

使用sass或less工具，方便对重复的样式进行提取并复用。

## 微信小程序机制

### 微信小程序API的兼容性

微信小程序提供的API存在迭代与废弃的情况，当使用的组件/API 的支持版本大于配置的线上最低基础库版本时，可能导致相应功能不可用。

官方文档相关内容：
https://developers.weixin.qq.com/miniprogram/dev/framework/compatibility.html

### JavaScript兼容性

我们需要注意小程序独特的JavaScript运行环境中的兼容性问题，我们可以通过下面的工具进行在线查询。

https://wechat-miniprogram.github.io/miniprogram-compat/

### 微信小程序API调用频率

小程序wx接口可分为“普通接口”和“限频接口”。“限频接口”指的是一个用户在一段时间内不允许频繁调用的wx接口，我们需要检查此类接口的调用频率，防止超出限制之后影响所有用户。

官方文档相关内容：

https://developers.weixin.qq.com/miniprogram/dev/framework/performance/api-frequency.html

### setData

注意setData调用的频率和数据大小，这将会影响小程序的性能：

https://developers.weixin.qq.com/miniprogram/dev/framework/audits/performance.html

### 第三方类库

注意第三方类库的兼容性，并注意打包过后的包体积大小，如果包体积过大则需要考虑是否使用其他方式而不是引用这个类库来解决问题。

## 其他

### 注释

保持必要的注释，特别在是小程序生态下，我们有时必须需要使用一些特殊且固定的数值进行计算，例如：

```typescript
// navBarHeight = this.data.navBarHeight + 10rpx(the margin bottom of nav bar)
// the height of homepageBanner image is 200rpx, the height of date-box is 88rpx， the height of notice bar is 80rpx
let height = windowHeight - (this.data.navBarHeight - 10 * scale) - 88 * scale;
```

### 审核

衡量此代码中的功能是否会影响小程序审核不通过。