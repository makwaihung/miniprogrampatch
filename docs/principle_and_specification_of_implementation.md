# miniprogrampatch 实现原则与规范

## 设计原则

minipogrampatch 实现的 `setData` 方法与微信小程序原有 API 输入与输出保持一致，不影响微信小程序现有任何功能，同时为 `Page` 和 `Component` 实例提供 `computed` 与 `watch` 能力。

## 实现规范

### 路径式属性名的解析规则

微信小程序 `setData({key: value})` 支持 key 为路径式字符串作为属性名称，miniprogrampath 针对路径式属性名称解析规则如下：

- 以句号`.`作为路径连接符，多个连续句号视为一个连接符。例如：`ab.c..d`，解析后的路径为`["ab", "c", "d"]`。
- 连接符出现路径字符串的首尾位置视为无效。例如：`...a.b.c...` 等同于 `a.b.c`。
- 允许空字符串作为路径名称中的一部分。例如：`a.b. c` 解析后为 `["a", "b", " c"]`。
- 数组路径以 `[]` 标识，数据路径符号中间只能是数字。例如：`a[1]` 或 `a[01]` 是正确的，`a[b]` 是非法的。
- 数组路径不能作为首位路径。例如：`[1].x` 是非法的。

#### 异常路径的解析

微信小程序中 `b[1]1]2]x` 会被解析成 `["b", "[1]", "[0]", "[0]", "12x"]`，miniprogrampatch 会解析成 `["b", "[1]", "1]2]x"]`。

异常路径的解析结果不同，可能会导致非预期的结果，所以需要尽量避免书写异常的路径名称。
