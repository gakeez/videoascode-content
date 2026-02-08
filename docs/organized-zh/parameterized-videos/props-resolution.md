---
image: /generated/articles-docs-how-props-flow.png
id: props-resolution
title: 属性如何解析
crumb: "参数化视频"
---

import {HowPropsFlow} from '../components/HowPropsFlow';

## 渲染期间

Remotion 在渲染视频时执行一个算法来确定传递给组件的属性。
三个因素发挥作用：

<Step>1</Step> <strong>默认属性</strong>是如果没有传递属性给渲染时的回退数据。你可以使用 <a href="/docs/composition#defaultprops"><code>defaultProps</code></a> 属性在你的 <a href="/docs/composition#defaultprops"><code>&lt;Composition /&gt;</code></a> 中指定它们。<br/>

<Step>2</Step> <strong>输入属性</strong>是调用渲染时传递的数据，可以通过 <a href="/docs/renderer/render-media#inputprops"><code>inputProps</code></a> 选项、<a href="/docs/cli/render#--props"><code>--props</code> 标志</a>或使用 Remotion Studio 中的渲染对话框传递。<br/>

<Step>3</Step> <a href="/docs/composition#calculatemetadata"><code>calculateMetadata()</code></a> 可用于动态转换属性，以及合成的元数据。

下图展示了属性如何解析：

<HowPropsFlow/>

## 在 Remotion Studio 中

在 Remotion Studio 中，属性以类似的方式解析，但有一些<strong>区别</strong>：

<Step>1</Step> 默认属性可以在<strong>右侧边栏</strong>中编辑。无效的修改将以红色轮廓标记，不会生效。<br/>
<Step>2</Step> 如果你使用 Render 按钮渲染视频，<strong>输入属性表单将预填充</strong>默认属性，包括右侧边栏中的修改。<br/><br/>

以下规则<strong>保持不变</strong>，你应该注意：

<Step>1</Step> 如果你使用 <a href="/docs/cli/studio#--props"><code>--props</code></a> 标志启动 Studio，这些数据将优先于默认属性，包括边栏中的修改。不建议向 Studio 传递输入属性。<br/>

<Step>2</Step> 传递的输入属性可能会被 <a href="/docs/composition#calculatemetadata"><code>calculateMetadata()</code></a> 转换。<br/>
