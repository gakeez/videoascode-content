---
image: /generated/articles-docs-audio-exporting.png
title: 导出音频
sidebar_label: 导出音频
id: exporting
crumb: '音频'
---

如果你从 Remotion 导出视频，音频会自动包含在内。
此外，本页展示了如何仅导出音频，或省略音频，以及导出过程的工作原理。

## 仅音频

支持导出为 `mp3`、`aac` 和 `wav`：

### 命令行

要通过 CLI 仅渲染音频，请在通过 CLI 导出时指定扩展名：

```sh
npx remotion render src/index.ts my-comp out/audio.mp3
```

或使用 `--codec` 标志自动选择良好的输出文件名：

```sh
npx remotion render src/index.ts my-comp --codec=mp3
```

### `renderMedia()`

要通过服务器端渲染 API 仅渲染音频，请使用 [`renderMedia()`](/docs/renderer/render-media) 并将 [`codec`](/docs/renderer/render-media#codec) 设置为音频编解码器。

```tsx twoslash title="render.js"
import {bundle} from '@remotion/bundler';
import {getCompositions, renderMedia} from '@remotion/renderer'; // 你要渲染的合成
import path from 'path';
const compositionId = 'HelloWorld';

// 你只需要执行一次，你可以重用 bundle。
const entry = './src/index';
console.log('Creating a Webpack bundle of the video');
const bundleLocation = await bundle(path.resolve(entry), () => undefined, {
  // 如果你有 Webpack 覆盖，请确保在这里添加它
  webpackOverride: (config) => config,
});

// 通过向组件传递任意 props 来参数化视频。
const inputProps = {
  foo: 'bar',
};

// 从 webpack bundle 中提取你在项目中定义的所有合成。
const comps = await getCompositions(bundleLocation, {
  // 你可以传递自定义输入 props，你可以使用 getInputProps() 在合成列表中检索它们。
  // 如果你想动态设置视频的持续时间或尺寸，请使用此方法。
  inputProps,
});

// 选择你要渲染的合成。
const composition = comps.find((c) => c.id === compositionId);

// 确保合成存在
if (!composition) {
  throw new Error(`No composition with the ID ${compositionId} found.
  Review "${entry}" for the correct ID.`);
}
const outputLocation = `out/${compositionId}.mp4`;

// ---cut---
await renderMedia({
  composition,
  serveUrl: bundleLocation,
  codec: 'mp3',
  outputLocation,
  inputProps,
});
```

### `renderMediaOnLambda()`

要通过 Lambda 仅渲染音频，请使用 [`renderMediaOnLambda()`](/docs/lambda/rendermediaonlambda) 并将 [`codec`](/docs/lambda/rendermediaonlambda#codec) 设置为音频编解码器，将 [`imageFormat`](/docs/lambda/rendermediaonlambda#imageformat) 设置为 `none`。

```tsx twoslash
import {renderMediaOnLambda} from '@remotion/lambda';
// ---cut---

const {bucketName, renderId} = await renderMediaOnLambda({
  region: 'us-east-1',
  functionName: 'remotion-render-bds9aab',
  composition: 'MyVideo',
  serveUrl: 'https://remotionlambda-qg35eyp1s1.s3.eu-central-1.amazonaws.com/sites/bf2jrbfkw',
  inputProps: {},
  codec: 'mp3',
  imageFormat: 'none',
});
```

### Lambda CLI

要通过 Lambda CLI 渲染，请使用 [`npx remotion lambda render`](/docs/lambda/cli/render) 命令并传递 [`--codec`](/docs/lambda/cli/render#--codec) 标志：

```sh
npx remotion lambda render --codec=mp3 https://remotionlambda-qg35eyp1s1.s3.eu-central-1.amazonaws.com/sites/bf2jrbfkw my-comp
```

## 排除音频

### 命令行

传递 `--muted` 以不导出音频。

```sh
npx remotion render --muted
```

### `renderMedia()`

传递 [`muted: true`](/docs/renderer/render-media#muted) 给 [`renderMedia()`](/docs/renderer/render-media) 以使渲染静音。

```tsx twoslash title="render.js"
import {bundle} from '@remotion/bundler';
import {getCompositions, renderMedia} from '@remotion/renderer'; // 你要渲染的合成
import path from 'path';
const compositionId = 'HelloWorld';

// 你只需要执行一次，你可以重用 bundle。
const entry = './src/index';
console.log('Creating a Webpack bundle of the video');
const bundleLocation = await bundle(path.resolve(entry), () => undefined, {
  // 如果你有 Webpack 覆盖，请确保在这里添加它
  webpackOverride: (config) => config,
});

// 通过向组件传递任意 props 来参数化视频。
const inputProps = {
  foo: 'bar',
};

// 从 webpack bundle 中提取你在项目中定义的所有合成。
const comps = await getCompositions(bundleLocation, {
  // 你可以传递自定义输入 props，你可以使用 getInputProps() 在合成列表中检索它们。
  // 如果你想动态设置视频的持续时间或尺寸，请使用此方法。
  inputProps,
});

// 选择你要渲染的合成。
const composition = comps.find((c) => c.id === compositionId);

// 确保合成存在
if (!composition) {
  throw new Error(`No composition with the ID ${compositionId} found.
  Review "${entry}" for the correct ID.`);
}
const outputLocation = `out/${compositionId}.mp4`;

// ---cut---
await renderMedia({
  composition,
  serveUrl: bundleLocation,
  codec: 'h264',
  muted: true,
  outputLocation,
  inputProps,
});
```

### `renderMediaOnLambda()`

传递 [`muted: true`](/docs/lambda/rendermediaonlambda#muted) 给 [`renderMediaOnLambda()`](/docs/lambda/rendermediaonlambda) 以使渲染静音。

```tsx twoslash
import {renderMediaOnLambda} from '@remotion/lambda';
// ---cut---

const {bucketName, renderId} = await renderMediaOnLambda({
  region: 'us-east-1',
  functionName: 'remotion-render-bds9aab',
  composition: 'MyVideo',
  serveUrl: 'https://remotionlambda-qg35eyp1s1.s3.eu-central-1.amazonaws.com/sites/bf2jrbfkw',
  inputProps: {},
  codec: 'h264',
  muted: true,
});
```

### Lambda CLI

传递 [`--muted`](/docs/lambda/cli/render#--muted) 给 [`npx remotion lambda render`](/docs/lambda/cli/render) 以在使用 Lambda 命令行时使渲染静音。

```sh
npx remotion lambda render --muted https://remotionlambda-qg35eyp1s1.s3.eu-central-1.amazonaws.com/sites/bf2jrbfkw my-comp
```
