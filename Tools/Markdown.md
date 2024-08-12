# Markdown

参考链接:

- [Typora](https://support.typoraio.cn/zh/Markdown-Reference/)
- [Github](https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github)



## 目录

[TOC]

## 转义

和编程语言一样，``Markdown` 也使用 `\` 进行转义

## 标题

```markdown
# A first-level heading
## A second-level heading
### A third-level heading
...
###### A sixth-level heading
```

## 文本样式

```markdown
<!-- 段落: 空白行 -->

<!-- 加粗 -->
**This is bold text**
__This is bold text__

<!-- 斜体 -->
_This text is italicized_
*This text is italicized*

<!-- 删除线 -->
~~This was mistaken text~~

<!-- 粗体和嵌入的斜体 -->
**This text is _extremely_ important**

<!-- 全部粗体和斜体 -->
***All this text is important***

<!-- 下标 -->
This is a ~subscript~ text

<!-- 上标 -->
This is a ^superscript^ text
```

## Code

inline: `git status`

block:

```javascript
function test() {
  console.log("notice the blank line before this function?");
}
```

## 图像

```markdown
![Screenshot of a comment on a GitHub issue showing an image, added in the Markdown, of an Octocat smiling and raising a tentacle.](https://myoctocat.com/assets/images/base-octocat.svg)
```

如果需要指定复杂的图像格式，推荐使用 `<img` 标签，视频同理

## 表格

```markdown
<!-- 注意表格前必须保留一个空白行 -->

| First Header  | Second Header |
| ------------- | ------------- |
| Content Cell  | Content Cell  |
| Content Cell  | Content Cell  |

| Command | Description |
| --- | --- |
| git status | List all new or modified files |
| git diff | Show file differences that haven't been staged |

| Left-aligned | Center-aligned | Right-aligned |
| :---         |     :---:      |          ---: |
| git status   | git status     | git status    |
| git diff     | git diff       | git diff      |
```

## 链接

```markdown
链接 [GitHub Pages](https://pages.github.com/)
相对链接 [Contribution guidelines for this project](docs/CONTRIBUTING.md)
```

## 列表

```markdown
<!-- 无序列表 -->
- George Washington
* John Adams
+ Thomas Jefferson

<!-- 有序列表 -->
1. James Madison
2. James Monroe
3. John Quincy Adams

<!-- 嵌套列表 -->
1. First list item
   - First nested list item
     - Second nested list item

<!-- 任务列表 -->
- [x] #739
- [ ] https://github.com/octo-org/octo-repo/issues/740
- [ ] Add delight to the experience when all tasks are complete :tada:
```

## 脚注

```text
Here is a simple footnote[^1].

A footnote can also have multiple lines[^2].

[^1]: My reference.
[^2]: To add line breaks within a footnote, prefix new lines with 2 spaces.
  This is a second line.
```

## 引用

```markdown
> Text that is a quote
```

## 警告

```markdown
> [!NOTE]
> Useful information that users should know, even when skimming content.

> [!TIP]
> Helpful advice for doing things better or more easily.

> [!IMPORTANT]
> Key information users need to know to achieve their goal.

> [!WARNING]
> Urgent info that needs immediate user attention to avoid problems.

> [!CAUTION]
> Advises about risks or negative outcomes of certain actions.
```

## 数学表达式

inline: $`\sqrt{3x-1}+(1+x)^2`$

block: 

$$\left( \sum_{k=1}^n a_k b_k \right)^2 \leq \left( \sum_{k=1}^n a_k^2 \right) \left( \sum_{k=1}^n b_k^2 \right)$$

在数学表达式中显示 `$` 需要转义

在普通文本中显示 `$` 可以使用 `span` 标签包含

## 折叠

参考链接: [折叠](https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/quickstart-for-writing-on-github#adding-a-collapsed-section)

## 关系图

### Mermaid

```mermaid
graph TD;
    A-->B;
    A-->C;
    B-->D;
    C-->D;
```

### GeoJSON 交互式地图

```geojson
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "id": 1,
      "properties": {
        "ID": 0
      },
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          [
              [-90,35],
              [-90,30],
              [-85,30],
              [-85,35],
              [-90,35]
          ]
        ]
      }
    }
  ]
}
```

### TopoJSON 地图

```topojson
{
  "type": "Topology",
  "transform": {
    "scale": [0.0005000500050005, 0.00010001000100010001],
    "translate": [100, 0]
  },
  "objects": {
    "example": {
      "type": "GeometryCollection",
      "geometries": [
        {
          "type": "Point",
          "properties": {"prop0": "value0"},
          "coordinates": [4000, 5000]
        },
        {
          "type": "LineString",
          "properties": {"prop0": "value0", "prop1": 0},
          "arcs": [0]
        },
        {
          "type": "Polygon",
          "properties": {"prop0": "value0",
            "prop1": {"this": "that"}
          },
          "arcs": [[1]]
        }
      ]
    }
  },
  "arcs": [[[4000, 0], [1999, 9999], [2000, -9999], [2000, 9999]],[[0, 0], [0, 9999], [2000, 0], [0, -9999], [-2000, 0]]]
}
```

### STL 3D 模型

```stl
solid cube_corner
  facet normal 0.0 -1.0 0.0
    outer loop
      vertex 0.0 0.0 0.0
      vertex 1.0 0.0 0.0
      vertex 0.0 0.0 1.0
    endloop
  endfacet
  facet normal 0.0 0.0 -1.0
    outer loop
      vertex 0.0 0.0 0.0
      vertex 0.0 1.0 0.0
      vertex 1.0 0.0 0.0
    endloop
  endfacet
  facet normal -1.0 0.0 0.0
    outer loop
      vertex 0.0 0.0 0.0
      vertex 0.0 0.0 1.0
      vertex 0.0 1.0 0.0
    endloop
  endfacet
  facet normal 0.577 0.577 0.577
    outer loop
      vertex 1.0 0.0 0.0
      vertex 0.0 1.0 0.0
      vertex 0.0 0.0 1.0
    endloop
  endfacet
endsolid
```
