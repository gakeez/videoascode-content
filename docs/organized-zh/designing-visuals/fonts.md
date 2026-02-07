---
image: /generated/articles-docs-fonts.png
title: 使用字体
sidebar_label: 字体
id: fonts
crumb: "技术"
---

以下是一些在 Remotion 中使用自定义字体的方法。

## 使用 `@remotion/google-fonts` 引入 Google Fonts

_从 v3.2.40 开始可用_

[`@remotion/google-fonts`](/docs/google-fonts) 是一种无需创建 CSS 文件即可加载 Google 字体的类型安全方式。

```tsx title="MyComp.tsx"
import { loadFont } from "@remotion/google-fonts/TitanOne";

const { fontFamily } = loadFont();

const GoogleFontsComp: React.FC = () => {
  return <div style={{ fontFamily }}>Hello, Google Fonts</div>;
};
```

## 使用 CSS 引入 Google Fonts

导入 Google Fonts 提供的 CSS。

:::note
从 2.2 版本开始，Remotion 会自动等待字体加载完成。
:::

```css title="font.css"
@import url("https://fonts.googleapis.com/css2?family=Bangers");
```

```tsx twoslash title="MyComp.tsx"
import "./font.css";

const MyComp: React.FC = () => {
  return <div style={{ fontFamily: "Bangers" }}>Hello</div>;
};
```

## 使用 `@remotion/fonts` 引入本地字体

_从 v4.0.164 开始可用_

将字体放入你的 `public/` 文件夹中。
在你的应用中调用 [`loadFont()`](/docs/fonts-api/load-font) 的位置确保它会执行：

```tsx twoslash title="load-fonts.ts"
import { loadFont } from "@remotion/fonts";
import { staticFile } from "remotion";

const fontFamily = "Inter";

loadFont({
  family: fontFamily,
  url: staticFile("Inter-Regular.woff2"),
  weight: "500",
}).then(() => {
  console.log("Font loaded!");
});
```

字体现在可以使用了：

```tsx twoslash title="MyComp.tsx"
const fontFamily = "Inter";

// ---cut---

<div style={{ fontFamily: fontFamily }}>Some text</div>;
```

## 手动引入本地字体

你可以使用原生的 [`FontFace`](https://developer.mozilla.org/en-US/docs/Web/API/FontFace) API 加载字体。

```tsx twoslash title="load-fonts.ts"
import { continueRender, delayRender, staticFile } from "remotion";

const waitForFont = delayRender();
const font = new FontFace(
  `Bangers`,
  `url('${staticFile("bangers.woff2")}') format('woff2')`,
);

font
  .load()
  .then(() => {
    document.fonts.add(font);
    continueRender(waitForFont);
  })
  .catch((err) => console.log("Error loading font", err));
```

:::note
如果你的 Typescript 类型报错，请安装最新版本的 `@types/web` 包。
:::
