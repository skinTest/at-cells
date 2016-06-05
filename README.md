# AtCell Usage

`atCell` 是一个针对移动端业务系统开发的具备一定灵活性而接口统一的组件。就组件而言分为两大部分。第一，单个 cell；第二，cells 组合以及遍历 cell。除了组件之外 atCell 也提供一些简单的数据提取工具和 cell 生成工具，现在集中在 AtCells 类内。

## index

1. stand alone Cell
    * [cell-common-api](#cell-common-api)
    * [cell-special-api](#cell-special-api)
    * [cell-structure](#cell-structure)
        - [at-static](#at-static)
        - [at-hidden](#at-hidden)
        - [at-input](#at-input)
        - [at-multi-input](#at-multi-input)
        - [at-single-select](#at-single-select)
        - [at-select](#at-select)
        - [at-left-select](#at-left-select)
        - [at-switch](#at-switch)
        - [at-textarea](#at-textarea)
        - [at-checkbox](#at-checkbox)
        - [at-radio](#at-radio)

2. [Cells](#at-cells)
    * [at-cells](#at-cells)


## cell-common-api

所有 at-cell 接受一个配置对象通过 cell 传入。该配置对象的数据结构随该组件绑定值的结构不同而变化，其中单 cell 单一值的占主体，嵌套值的主要有 `['at-multi-input', 'at-left-select']` 下表是常用字段的意义。

-------------------------------

  字段名 | 数据类型 | 作用 | 备注
  ------|---------|------|----------------
`is`      | String | 这是一个 vue 的 directive，与 component 元素结合使用，动态渲染组件。在 at-pure-cells 的使用中，需要对每个 cell 的配置对象进行配置。_为了后续表述方便，约定，以`cell.is`指代元素cell类型_ | `at-pure-cells`
`switch`      | Booleand | 当为 `false` 时元素不渲染 | 失效：`['at-hidden']`
`label`       | String   | cell 左边的显示内容，或是选项的显示内容 | 同上
`value`       |    *     | 实现对 cell 的数据动态绑定，同时在 value 变化时向外提供操作 vue instance 的接口 | 特殊结构：`['at-multi-input', 'at-left-select']`
`name`        | String   | 提供与后端数据进行对应的标识，需要注意的是，name 与 value 成对出现 | 同上
`dispatch`    | String   | 为调用 at-cell 的 vue 组件提供 events 监听接口，dispatch 与 value 对应。 | 同上
`debounce`    | Number   | 适用于需要手动输入内容的元素，为值的改变提供缓冲时间，降低对 `cell.value` 监听的回调触发频率 | 有效的 cell：``(/input|left\-selet/).test(cell.is)``
`type`        | String   | `<input :type="cell.type">` | 同上
`placeholder` | String   | `<input :placeholder="cell.placeholder">` atCell 同时为最常用的 `at-select` 也提供了 placeholder | 有效的 cell： ``(/input|select/).test(cell.is)``
`unit`        | String   | 为输入框提供后缀的字符 | `(/input|left-select/).test(cell.is)`
`options`     | Array    | 为 select 元素提供选项配置，为 switch 元素提供数据输出转转译接口，checkbox 与 radio 的显示条目。除 switch 外的 option 单元为一个由 label 和 value 字段组成的对象， switch 中 option 单元形式不定。 | 有效的 cell： ``!(/input/).test(cell.is)``

## cell-special-api

cell.is | api | Usage | Note
--------|-----|-------|-----
`at-static`        | `content` | 用于渲染 cell 右侧的内容 |
                   | `header`  | Boolean, 为 true 时不显示下边线
`at-hidden`        | | |
`at-input`  | `type` | | 可用的值 `['text', 'number', 'date', 'datetime-local']`, 同时适用其他有 input 单元的 cell
`at-multi-input`   | `inputs` | Array, [unit shape](#at-multi-input) |
`at-single-select` | | |
`at-select`  | | |
`at-left-select`   | `input` | Object, [shape](#at-left-select) |
`at-switch`        | `options` | 为了方便 cell 同步数据，array 的第一个值代表 switch 打开，显示 true 的情况，第二个值反之 |
`at-textarea`      | `rows`  | Number: 输入框的行数 | 『MUST』传入
                   | `limit` | Number: 输入框的字数限制 | 可省略，传入时当输入量大于其值时会切割字符串
`at-checkbox`      | | | 1. 可以与 header 为 true 的 `at-static` 结合使用; 2. 其 value 一定时 Array 类型
`at-radio`         | | | 可以与 header 为 true 的 `at-static` 结合使用

## cell-structure

每个 cell 所传入配置对象的 shape

### at-static

静态 cell。但也拥有数据接口 value，字段均可省略，但不建议全部省略，全部省略请选择 at-hidden

```js
{
    label: String,
    content: String,
    header: Boolean,
    value: *,
    name: String,
    dispatch: String,
    switch: Boolean
}
```

### at-hidden

隐藏 cell。其出现的原因就是为了 name 与 value

```js
{
    value: *,
    name: String,
    dispatch: String
}
```

### at-input

左标题，右侧单输入框，单位可自定义

```js
{
    label: String,
    switch: Boolean,
    // 表单部分
    name: String,
    value: *,
    type: String,
    placeholder: {
        type: String,
        required: false
    },
    unit: {
        type: String,
        pattern: length <= 2,
        required: false
    },
    // 事件监听部分
    dispatch: String,
    debounce: Number
}
```

### at-multi-input

单 cell 多输入框

```js
{
    label: String,
    switch: Boolean,
    inputs: [
        {
            // 表单部分
            name: String,
            value: *,
            type: String,
            placeholder: {
                type: String,
                required: false
            },
            unit: {
                type: String,
                pattern: length <= 2,
                required: false
            },
            // 事件监听部分
            dispatch: String
            debounce: Number,
        },
        {...},
        {...}
    ]
}
```

### at-select

左侧标题，右侧输入框

```js
{
    label: String,
    switch: Boolean,
    // 事件监听部分
    dispatch: String,
    debounce: Number
    // 表单部分
    name: String,
    value: *,
    type: String,
    placeholder: {
        type: String,
        required: false
    },
    options: [
        {
            label: String,
            value: *
        },
        {...}
    ]
}
```

### at-single-select

右对齐选择框

```js
{
    switch: Boolean,
    // 事件监听部分
    dispatch: String,
    debounce: Number
    // 表单部分
    name: String,
    value: *,
    type: String,
    placeholder: {
        type: String,
        required: false
    },
    options: [
        {
            label: String,
            value: *
        },
        {...}
    ]
}
```


### at-left-select

左侧选择，右侧输入

```js
{
    switch: Boolean,
    // 输入部分
    input: {
        name: String,
        value: *,
        type: String,
        placeholder: {
            type: String,
            required: false
        },
        unit: {
            type: String,
            pattern: length <= 2,
            required: false
        },
        dispatch: String
        debounce: Number,
    },
    // 选择部分
    dispatch: String,
    debounce: Number
    name: String,
    value: *,
    type: String,
    placeholder: {
        type: String,
        required: false
    },
    options: [
        {
            label: String,
            value: *
        },
        {...}
    ]
}
```

### at-switch

本质上是一个单行 radio

```js
{
    switch: Boolean,
    label: String,
    // 事件监听部分
    dispatch: String,
    // 表单部分
    name: String,
    value: *,
    type: String,
    options: [*, ...]
}
```

### at-textarea

当监听 value 时 『SHOULD』 使用 debounce, 需要内容提示时建议使用 at-static with cell.header be true

```js
{
    switch: Boolean,
    // 表单部分
    name: String,
    value: *,
    rows: Number,
    limit: Number,
    placeholder: {
        type: String,
        required: false
    },
    // 事件监听部分
    dispatch: String,
    debounce: Number
}
```

### at-checkbox

实质是多个 label 标签的遍历。

```js
{
    label:  String,
    switch: Boolean,
    // 事件监听部分
    dispatch: String,
    debounce: Number
    // 表单部分
    name: String,
    value: Array,
    type: String,
    options: [
        {
            label: String,
            value: *
        },
        {...}
    ]
}
```

### at-radio

实质是多个 label 标签的遍历。

```js
{
    label:  String,
    switch: Boolean,
    // 事件监听部分
    dispatch: String,
    debounce: Number
    // 表单部分
    name: String,
    value: *,
    type: String,
    options: [
        {
            label: String,
            value: *
        },
        {...}
    ]
}
```

--------------------------------


## Cells

一些对单一 cell 的遍历形式

### at-cells

```html
<at-cells :cells="[{...}, {...}]"></at-cells>
```

其接口是一个由单一 cell 配置单元组成的数组。除了相应 cell 自身的配置内容外还需要配置 is 字段。同时，`at-cells` 提供了简单的 cell 间监听开关。

```js
{
    is: String,    // 用于 cell 类型判定，必填，不填报错加不渲染
    toggle: {      // 一种简易的开关形式，只支持对其他元素的单次关联，行
        offset: Number,                        // 用于定位监听元素， index - offset
        key:    String,                        // 用于确定监听的字段，嵌套 value 请进一步开发
        value: *                               // when
        compare: fucntion (cells, index)       // 详见 compare
    }
}
```

__compare__

_parameters_

    cells
    用于遍历的原始对象

    index
    本 cell 在队列中的序号

_return_

    true
    cell 显示

    false
    cell 关闭
