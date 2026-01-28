---
name: json-canvas
description: 创建和编辑 JSON Canvas 文件（.canvas），支持节点、连线、分组和连接。用于处理 .canvas 文件、创建可视化画布、思维导图、流程图，或当用户提到 Obsidian 中的 Canvas 文件时使用。
---

# JSON Canvas 技能

本技能使 AI 智能体能够创建和编辑有效的 JSON Canvas 文件（`.canvas`），用于 Obsidian 和其他应用程序。

## 概述

JSON Canvas 是一种开放的无限画布数据文件格式。Canvas 文件使用 `.canvas` 扩展名，包含符合 [JSON Canvas Spec 1.0](https://jsoncanvas.org/spec/1.0/) 规范的有效 JSON。

## 文件结构

Canvas 文件包含两个顶级数组：

```json
{
  "nodes": [],
  "edges": []
}
```

- `nodes`（可选）：节点对象数组
- `edges`（可选）：连接节点的边对象数组

## 节点

节点是放置在画布上的对象。有四种节点类型：
- `text` - 带 Markdown 的文本内容
- `file` - 文件/附件引用
- `link` - 外部 URL
- `group` - 其他节点的视觉容器

### Z-Index 排序

节点按数组中的 z-index 排序：
- 第一个节点 = 底层（显示在其他节点下方）
- 最后一个节点 = 顶层（显示在其他节点上方）

### 通用节点属性

所有节点共享以下属性：

| 属性 | 必需 | 类型 | 说明 |
|------|------|------|------|
| `id` | 是 | string | 节点的唯一标识符 |
| `type` | 是 | string | 节点类型：`text`、`file`、`link` 或 `group` |
| `x` | 是 | integer | X 坐标（像素） |
| `y` | 是 | integer | Y 坐标（像素） |
| `width` | 是 | integer | 宽度（像素） |
| `height` | 是 | integer | 高度（像素） |
| `color` | 否 | canvasColor | 节点颜色（见颜色部分） |

### 文本节点

文本节点包含 Markdown 内容。

```json
{
  "id": "6f0ad84f44ce9c17",
  "type": "text",
  "x": 0,
  "y": 0,
  "width": 400,
  "height": 200,
  "text": "# 你好世界\n\n这是 **Markdown** 内容。"
}
```

| 属性 | 必需 | 类型 | 说明 |
|------|------|------|------|
| `text` | 是 | string | 带 Markdown 语法的纯文本 |

### 文件节点

文件节点引用文件或附件（图片、视频、PDF、笔记等）。

```json
{
  "id": "a1b2c3d4e5f67890",
  "type": "file",
  "x": 500,
  "y": 0,
  "width": 400,
  "height": 300,
  "file": "附件/图表.png"
}
```

| 属性 | 必需 | 类型 | 说明 |
|------|------|------|------|
| `file` | 是 | string | 系统内的文件路径 |
| `subpath` | 否 | string | 链接到标题或块（以 `#` 开头） |

### 链接节点

链接节点显示外部 URL。

```json
{
  "id": "c3d4e5f678901234",
  "type": "link",
  "x": 1000,
  "y": 0,
  "width": 400,
  "height": 200,
  "url": "https://obsidian.md"
}
```

| 属性 | 必需 | 类型 | 说明 |
|------|------|------|------|
| `url` | 是 | string | 外部 URL |

### 分组节点

分组节点是用于组织其他节点的视觉容器。

```json
{
  "id": "d4e5f6789012345a",
  "type": "group",
  "x": -50,
  "y": -50,
  "width": 1000,
  "height": 600,
  "label": "项目概览",
  "color": "4"
}
```

| 属性 | 必需 | 类型 | 说明 |
|------|------|------|------|
| `label` | 否 | string | 分组的文本标签 |
| `background` | 否 | string | 背景图片路径 |
| `backgroundStyle` | 否 | string | 背景渲染样式 |

#### 背景样式

| 值 | 说明 |
|------|------|
| `cover` | 填充节点的整个宽度和高度 |
| `ratio` | 保持背景图片的宽高比 |
| `repeat` | 在两个方向上重复图片作为图案 |

## 边（连线）

边是连接节点的线条。

```json
{
  "id": "f67890123456789a",
  "fromNode": "6f0ad84f44ce9c17",
  "toNode": "a1b2c3d4e5f67890"
}
```

| 属性 | 必需 | 类型 | 默认值 | 说明 |
|------|------|------|--------|------|
| `id` | 是 | string | - | 边的唯一标识符 |
| `fromNode` | 是 | string | - | 连接起始节点的 ID |
| `fromSide` | 否 | string | - | 边起始的一侧 |
| `fromEnd` | 否 | string | `none` | 边起始端的形状 |
| `toNode` | 是 | string | - | 连接目标节点的 ID |
| `toSide` | 否 | string | - | 边结束的一侧 |
| `toEnd` | 否 | string | `arrow` | 边结束端的形状 |
| `color` | 否 | canvasColor | - | 线条颜色 |
| `label` | 否 | string | - | 边的文本标签 |

### 边的方向值

| 值 | 说明 |
|------|------|
| `top` | 节点顶部 |
| `right` | 节点右侧 |
| `bottom` | 节点底部 |
| `left` | 节点左侧 |

### 端点形状

| 值 | 说明 |
|------|------|
| `none` | 无端点形状 |
| `arrow` | 箭头端点 |

## 颜色

`canvasColor` 类型可以通过两种方式指定：

### 十六进制颜色

```json
{
  "color": "#FF0000"
}
```

### 预设颜色

```json
{
  "color": "1"
}
```

| 预设 | 颜色 |
|------|------|
| `"1"` | 红色 |
| `"2"` | 橙色 |
| `"3"` | 黄色 |
| `"4"` | 绿色 |
| `"5"` | 青色 |
| `"6"` | 紫色 |

## ID 生成

节点和边的 ID 必须是唯一字符串。Obsidian 生成 16 字符的十六进制 ID：

```json
"id": "6f0ad84f44ce9c17"
```

## 布局指南

### 定位

- 坐标可以为负值（画布无限延伸）
- `x` 向右增加
- `y` 向下增加
- 位置指节点的左上角

### 推荐尺寸

| 节点类型 | 建议宽度 | 建议高度 |
|----------|----------|----------|
| 小文本 | 200-300 | 80-150 |
| 中文本 | 300-450 | 150-300 |
| 大文本 | 400-600 | 300-500 |
| 文件预览 | 300-500 | 200-400 |
| 链接预览 | 250-400 | 100-200 |
| 分组 | 根据内容 | 根据内容 |

### 间距

- 分组内部留 20-50px 内边距
- 节点之间间隔 50-100px 以保持可读性
- 将节点对齐到网格（10 或 20 的倍数）以获得更整洁的布局

## 验证规则

1. 所有 `id` 值必须在节点和边之间唯一
2. `fromNode` 和 `toNode` 必须引用现有的节点 ID
3. 每种节点类型必须包含必需字段
4. `type` 必须是：`text`、`file`、`link`、`group` 之一
5. `backgroundStyle` 必须是：`cover`、`ratio`、`repeat` 之一
6. `fromSide`、`toSide` 必须是：`top`、`right`、`bottom`、`left` 之一
7. `fromEnd`、`toEnd` 必须是：`none`、`arrow` 之一
8. 颜色预设必须是 `"1"` 到 `"6"` 或有效的十六进制颜色

## 参考

- [JSON Canvas Spec 1.0](https://jsoncanvas.org/spec/1.0/)
- [JSON Canvas GitHub](https://github.com/obsidianmd/jsoncanvas)
