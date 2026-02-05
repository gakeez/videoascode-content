---
image: /generated/articles-docs-preview.png
title: 预览你的视频
id: preview
crumb: '时间线基础'
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

你可以通过启动 [Remotion Studio](/docs/studio) 来预览你的视频：

<Tabs
defaultValue="Regular templates"
values={[
{ label: 'Regular templates', value: 'Regular templates', },
{ label: 'Next.js and React Router 7 templates', value: 'Next.js and React Router templates', },
]
}>
<TabItem value="Regular templates">

```bash
npm run dev
```

  </TabItem>

  <TabItem value="Next.js and React Router templates">

```bash
npm run remotion
```

  </TabItem>
</Tabs>

这是 [Remotion CLI](/docs/cli) 的 [`studio`](/docs/cli/studio) 命令的简写：

```bash
npx remotion studio
```

服务器将在端口 `3000`（或更高的可用端口）启动，Remotion Studio 应该在浏览器中打开。

<img src="/img/timeline.png"></img>

了解更多关于 [Remotion Studio](/docs/studio) 的信息。

:::note
在旧项目中，`npm start` 曾被用来代替 `npm run dev`。
:::
