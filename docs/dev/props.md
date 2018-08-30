---
sidebarDepth: 2
---

# Props

## 布局类

### headerTitle

- type: `String`
- default: `''`

设置页面顶部通栏内的页面标题文本。

### headerNav

- type: `Array`
- default: `[]`

设置页面顶部通栏内的面包屑，每一个数组项支持属性有：

| 参数   | 说明                                      |
| ------ | ----------------------------------------- |
| `text` | 显示文字                                  |
| `to`   | 可选，路由跳转对象，同 vue-router 的 `to` |

### height

- type: `[String, Number]`
- default: `null`

优先级**最高**，设置整体布局高度，包含顶部标题栏、搜索栏、正文区域、页码区域所有内容的高度，支持百分比。

### fullHeight

- type: `Boolean`
- default: `true`

垂直高度是否铺满屏幕高度。

### contentMinHeight

- type: `[String, Number]`
- default: `160`

内容区域最小高度。在 `fullHeight` 为 `false` 的时候生效。

## 搜索栏

### filterButtons

- type: `Array`
- default: `[]`

搜索栏左侧按钮配置，具体可查看 [《Prop filterButtons》](prop-filter-buttons.md) 。

### filterFields

- type: `Array`
- default: `[]`

搜索栏搜索字段配置，具体可查看 [《Prop filterFields》](prop-filter-fields.md) 。

### filterModel

- type: `Object`
- default: `{}`

可选，存储搜索栏的搜索条件值。如果有需要跟随请求直接发送的数据也可在此设置，以实现类似“隐藏域”的提交效果。

### showFilterSearch

- type: `Boolean`
- default: `true`

是否显示搜索栏的“提交”按钮。

### showFilterReset

- type: `Boolean`
- default: `true`

是否显示搜索栏的“重置”按钮。

## 分页

### usePage

- type: `Boolean`
- default: `true`

底部是否显示分页功能。开启后，发送接口的参数除了包含搜索栏内的数据，还会自动附加上 `page` 和 `pageSize` 2 个参数。

### pageSizes

- type: `Array`
- default: `[20, 50, 100]`

分页“每页数量”可选值。

### pageSize

- type: `Number`
- default: `20`

默认每页分页数量。

## 表格

### tableColumns

- type: `Array`
- default: `[]`

表格列配置，具体可查看 [《Prop tableColumns》](prop-table-columns.md)。

### tableProps

- type: `Object`
- default: `{}`

可传入 [《Element-UI table#table-attributes》](http://element.eleme.io/#/zh-CN/component/table#table-attributes) 的所有属性。

内部通过 `v-bind` 绑定给 `<el-table>` 元素。

### tableEvents

- type: `Object`
- default: `{}`

可传入 [《Element-UI table#table-events》](http://element.eleme.io/#/zh-CN/component/table#table-events) 的所有支持事件。

内部通过 `v-on` 绑定给 `<el-table>` 元素。

### tableSelectEnable

- type: `Boolean`
- default: `true`

是否开启表格行选择功能。

### tableSelection

- type: `Array`
- default: `[]`

表格行选择的选中数据。可通过 `.sync` 修饰符获取更新：

```vue
<listview :table-selection.sync="tableSelection" />
```

## 数据请求

::: tip 说明

每次发起请求会自动将 [filterModel](#filtermodel) 中的数据作为参数提交。

:::

### autoload

- type: `Boolean`
- default: `true`

初始化后是否自动加载第一页内容。

### requestUrl

- type: `String`
- default: `''`

数据请求接口地址。

### requestMethod

- type: `String`
- default: `get`

支持 Axios 支持的方法： `get`, `delete`, `head`, `options`, `post`, `put`, `patch` 。

### requestConfig

- type: `Object`
- default: `{}`

兼容 Axios 的所有 [`requestConfig`](https://github.com/axios/axios#request-config) 配置，除了 `cancelToken` 。

## 错误处理

### validateResponse

- type: `Function`
- default:

  ```js
  function (response) {
    try {
      return response.data.is_success
    } catch (e) {
      return false
    }
  }
  ```

验证接口响应是否成功。若接口响应格式字段有差异，可修改该配置，如果无需错误处理可直接返回 `true` 。

### resolveRequestErrorMessage

- type: `Function`
- default:

  ```js
  function (response) {
    try {
      return response.data.error_info.msg
    } catch (e) {
      return '未知错误'
    }
  }
  ```

在 `validateResponse` 返回 `false` 表示请求失败后，会调用 `resolveRequestErrorMessage` 解析错误提示信息。

- 【默认表格样式】错误信息会出现在表格内容区域内
- 【自定义 slot 】数据挂载在 slot-scope 的 `content-message` 属性上。

### contentMessage

- type: `[Object, String]`
- default: `{}`

可用在 [autoload](#autoload) 为 `false` 时候，初始显示的提示信息。

如果为 `String` 类型则不带图标只显示文本。如果为 `Object` 类型则支持格式为 `{ type: '', message: '' }` ，其中 type 支持 `success` , `warning` , `info` , `error` 。

## 数据请求 - 高级配置

::: tip 说明

除非接口参数、响应格式有特殊需求，常规场景使用一般无需更改这一小节内的配置。

:::

如果对于数据接口请求在发送前、接受后有特殊的处理流程，或者接口数据格式与规范不一致，可通过以下几个参数对数据进行加工处理，内部处理流程为：

<!-- ./request-flow.puml -->

![request-flow](./request-flow.svg)

### requestHandler

- type: `Function(requestData)`
- default: `null`

自定义请求方法，该方法优先级最高，若设置了该方法，其他请求相关的配置以及 `validateResponse` 错误验证，直接将方法返回值使用 `contentDataMap` 进行映射处理，支持返回 Promise 。

### transformRequestData

- type: `Function(requestData)`
- default: `null`

该方法可对接口发起请求参数在发送前作最后的更改。参数 `requestData` 包含搜索栏的所有数据，如果有开启分页还会包含 `page` 和 `pageSize` 。

### transformResponseData

- type: `Function`
- default: `null`

### contentDataMap

- type: `Object`
- default: `{ items: 'result.items', total: 'result.total' }`

数据接口响应内容属性映射。可以直接配置各属性相对于接口响应数据的取值路径来直接映射返回值。默认会有表格视图所需的 2 个属性映射 `items` （表格数据） 和 `total` 用于分页组件。

在发起请求并判断接口获取成功（[`validateResponse`](#validateresponse) 方法验证通过）后：

- 【默认表格样式】会分别在表格数据和分页组件使用 `items` 和 `total` 2 个属性。
  - `<el-table :data="contentData.items" />`
  - `<el-pagination :total="contentData.total" />`
- 【自定义 slot 】数据挂载在 slot-scope 的 `content-data` 属性上。
- **如果 `contentDataMap` 设置为 `null` ，则不进行映射处理，直接返回接口响应数据。**

例如：有接口响应为

```js
{
  is_success: true,
  result: {
    items: [1, 2, 3],
    total: 20
  }
}
```

通过映射表配置

```js
{
  items: 'result.items',
  total: 'result.total',
  status: 'is_success'
}
```

可得到：

```js
{
  items: [1, 2, 3],
  total: 20,
  status: true
}
```