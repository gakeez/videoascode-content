---
image: /generated/articles-docs-parameterized-rendering.png
id: parameterized-rendering
title: 参数化视频
sidebar_label: 传递属性
crumb: "操作指南"
---

Remotion 支持对视频参数化所需的数据进行[引入](/docs/passing-props)、[验证](/docs/schemas)、[可视化编辑](/docs/visual-editing)和转换。

数据可以[影响视频内容](/docs/data-fetching)，或者影响视频的[元数据](/docs/dynamic-metadata)，如宽度、高度、时长或帧率。

## 高级概述

Remotion 允许向 React 组件传递[属性（props）](https://react.dev/learn/passing-props-to-a-component)。  
Props 是 React 的概念，采用 JavaScript 对象的形式。

要确定传递给视频的数据，需要执行以下步骤：

<Step>1</Step> <strong>默认属性</strong>是静态定义的，这样 Studio 中无需任何数据即可设计视频。<br/>

<ul>
<li>
默认属性定义了数据的结构。
</li>
<li>
可以定义和验证 Schema。
</li>
<li>
在没有数据的情况下，可以在 Remotion Studio 中编辑默认属性。
</li>
</ul>
<Step>2</Step> <strong>输入属性</strong>可以在渲染视频时指定，以覆盖默认属性。<br/>
<ul>
<li>
输入属性将与默认属性合并，输入属性具有优先权。
</li>
</ul>

<Step>3</Step> <strong>使用 <a href="/docs/data-fetching"><code>calculateMetadata()</code></a></strong>，可以对属性进行后处理并动态计算元数据。<br/>

<ul>
<li>
例如，如果传递了 URL 作为属性，可以获取其内容并添加到属性中。
</li>
<li>
也可以在这里异步计算视频时长和其他元数据。
</li>
</ul>
<Step>4</Step> <strong>最终属性</strong>被传递给 React 组件。
<ul>
<li>
组件可以根据属性动态渲染内容。
</li>
</ul>

有关解析流程工作原理的可视化说明和更多详细信息，请参阅[这里](/docs/props-resolution)。

## 目录

- [传递属性](/docs/passing-props)
- [定义 Schema](/docs/schemas)
- [可视化编辑](/docs/visual-editing)
- [数据获取](/docs/data-fetching)
- [可变元数据](/docs/dynamic-metadata)
- [属性如何解析](/docs/props-resolution)

## 另请参阅

你可以使用 [Remotion Player](/docs/player) 在 React 应用中显示 Remotion 组件并动态更改内容，而无需渲染视频，从而创建内容实时更新的体验。
