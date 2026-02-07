---
image: /generated/articles-docs-importing-assets.png
id: assets
title: 导入资源
sidebar_label: 资源
crumb: '操作指南'
---

要在 Remotion 中导入资源，在你的项目中创建一个 `public/` 文件夹，并使用 [`staticFile()`](/docs/staticfile) 来导入它。

```txt
my-video/
├─ node_modules/
├─ public/
│  ├─ logo.png
├─ src/
│  ├─ MyComp.tsx
│  ├─ Root.tsx
│  ├─ index.ts
├─ package.json
```

```tsx twoslash title="src/MyComp.tsx"
import {Img, staticFile} from 'remotion';

export const MyComp: React.FC = () => {
  return <Img src={staticFile('logo.png')} />;
};
```

## 使用图片

使用 Remotion 的 [`<Img/>`](/docs/img) 标签。

```tsx twoslash title="MyComp.tsx"
import {Img, staticFile} from 'remotion';

export const MyComp: React.FC = () => {
  return <Img src={staticFile('logo.png')} />;
};
```

你也可以传入 URL：

```tsx twoslash title="MyComp.tsx"
import {Img} from 'remotion';

export const MyComp: React.FC = () => {
  return <Img src="https://picsum.photos/id/237/200/300" />;
};
```

## 使用图像序列

如果你有一系列图像，例如从 After Effects 或 Rotato 等其他程序导出的，你可以通过插值路径来创建动态导入。

```txt
my-video/
├─ public/
│  ├─ frame1.png
│  ├─ frame2.png
│  ├─ frame3.png
├─ package.json
```

```tsx twoslash
import {Img, staticFile, useCurrentFrame} from 'remotion';

const MyComp: React.FC = () => {
  const frame = useCurrentFrame();

  return <Img src={staticFile(`/frame${frame}.png`)} />;
};
```

## 使用视频

使用 [`<OffthreadVideo />`](/docs/offthreadvideo)、[`<Video />`](/docs/media/video) 或 [`<Html5Video />`](/docs/html5-video) 组件来保持时间线和视频同步。

```tsx twoslash
import {OffthreadVideo, staticFile} from 'remotion';

export const MyComp: React.FC = () => {
  return <OffthreadVideo src={staticFile('vid.webm')} />;
};
```

通过 URL 加载视频也是可能的：

```tsx twoslash
import {OffthreadVideo} from 'remotion';

export const MyComp: React.FC = () => {
  return <OffthreadVideo src="https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/BigBuckBunny.mp4" />;
};
```

另请参阅：[Remotion 支持哪些视频格式？](/docs/miscellaneous/video-formats)

## 使用音频

使用 [`<Html5Audio>`](/docs/html5-audio) 组件。

```tsx twoslash
import {Html5Audio, staticFile} from 'remotion';

export const MyComp: React.FC = () => {
  return <Html5Audio src={staticFile('tune.mp3')} />;
};
```

从 URL 加载音频也是可能的：

```tsx twoslash
import {Html5Audio} from 'remotion';

export const MyComp: React.FC = () => {
  return <Html5Audio src="https://file-examples.com/storage/fe48a63c5264cbd519788b3/2017/11/file_example_MP3_700KB.mp3" />;
};
```

查看[音频指南](/docs/using-audio)了解如何包含音频。

## 使用 CSS

将 .css 文件放在 JavaScript 源文件旁边，并使用 `import` 语句。

```txt
my-video/
├─ node_modules/
├─ src/
│  ├─ style.css
│  ├─ MyComp.tsx
│  ├─ Root.tsx
│  ├─ index.ts
├─ package.json
```

```tsx twoslash title="MyComp.tsx"
import './style.css';
```

:::note
想要使用 SASS、Tailwind 或类似工具？[查看如何覆盖 Webpack 配置的示例](/docs/webpack)。
:::

## 使用字体

[阅读字体的单独页面。](/docs/fonts)

## `import` 语句

作为导入文件的替代方式，Remotion 允许你在项目中 `import` 或 `require()` 几种类型的文件：

- 图片（`.png`、`.svg`、`.jpg`、`.jpeg`、`.webp`、`.gif`、`.bmp`）
- 视频（`.webm`、`.mov`、`.mp4`）
- 音频（`.mp3`、`.wav`、`.aac`、`.m4a`）
- 字体（`.woff`、`.woff2`、`.otf`、`.ttf`、`.eot`）

例如：

```tsx twoslash title="MyComp.tsx"
import {Img} from 'remotion';
import logo from './logo.png';

export const MyComp: React.FC = () => {
  return <Img src={logo} />;
};
```

### 注意事项

虽然这以前是导入文件的主要方式，但由于以下原因，我们现在不推荐使用：

- 只支持上面列出的文件扩展名。
- 最大文件大小为 2GB。
- 动态导入如 `require('img' + frame + '.png')` 是[有问题的](/docs/webpack-dynamic-imports)。

如果可能，优先使用 [`staticFile()`](/docs/staticfile) 导入。

## 基于资源的动态时长

要使视频时长依赖于资源，请参阅：[动态时长、FPS 和尺寸](/docs/dynamic-metadata)

## 项目外的文件

Remotion 在浏览器中运行，因此无法访问你电脑上的任意文件。
也不能在浏览器中使用 Node.js 的 `fs` 模块。
相反，将资源放在 `public/` 文件夹中，并使用 [`getStaticFiles()`](/docs/getstaticfiles) 来枚举它们。

查看[为什么 Remotion 不支持绝对路径](/docs/miscellaneous/absolute-paths)。

## 打包后添加资源

在渲染之前，代码使用 Webpack 进行打包，只有打包后的资源才能在之后访问。
因此，在调用 [`bundle()`](/docs/bundle) 之后添加到 public 文件夹的资源将无法在渲染期间访问。
但是，如果你使用[服务端渲染 API](/docs/ssr-node)，你可以在打包后将资源添加到 bundle 内的 `public` 文件夹中。

## 使用 `<Img>`、`<Video>` 和 `<Audio>`

**使用 [`<Img />`](/docs/img) 或 [`<Gif />`](/docs/gif)** 而不是原生的 `<img>` 标签、Next.js 的 `<Image>` 和 CSS `background-image`。
**使用 [`<OffthreadVideo />`](/docs/offthreadvideo)、[`<Video />`](/docs/media/video) 或 [`<Html5Video />`](/docs/html5-video)** 而不是原生的 `<video>` 标签。
**使用 [`<Audio />`](/docs/media/audio) 或 [`<Html5Audio />`](/docs/html5-audio)** 而不是原生的 `<audio>` 标签。
**使用 [`<IFrame />`](/docs/iframe)** 而不是原生的 `<iframe>` 标签。

<br />

通过使用 Remotion 的组件，你可以确保：

<Step>1</Step> 资源在渲染帧之前完全加载
<br />
<Step>2</Step> 图片和视频与 Remotion 的时间线同步。

## 另请参阅

- [staticFile()](/docs/staticfile)
- [getStaticFiles()](/docs/getstaticfiles)
- [watchStaticFile()](/docs/watchstaticfile)
- [为什么 Remotion 不支持绝对路径](/docs/miscellaneous/absolute-paths)
