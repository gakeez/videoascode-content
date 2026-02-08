---
image: /generated/articles-docs-passing-props.png
id: passing-props
title: 向合成传递属性
sidebar_label: 传递属性
crumb: '操作指南'
---

```twoslash include example
type Props = {
  propOne: string;
  propTwo: number;
}

export const MyComponent: React.FC<Props> = ({propOne, propTwo}) => {
  return (
    <div>props: {propOne}, {propTwo}</div>
  );
}
// - MyComponent
```

你可以使用 [React 属性（"props"）](https://react.dev/learn/passing-props-to-a-component)来参数化视频内容。

## 定义接受的属性

要定义你的视频接受哪些属性，请给组件 `React.FC` 类型并传入一个泛型参数，描述你要接受的属性结构：

```tsx twoslash title="src/MyComponent.tsx"
// @include: example-MyComponent
```

## 定义默认属性

当注册一个接受属性的组件作为 Composition 时，你必须定义默认属性：

```tsx twoslash {14-17} title="src/Root.tsx"
// organize-imports-ignore

// @filename: MyComponent.tsx
import React from 'react';
export const MyComponent: React.FC<{
  propOne: string;
  propTwo: number;
}> = () => null;

// @filename: Root.tsx

// ---cut---
import React from 'react';
import {Composition} from 'remotion';
import {MyComponent} from './MyComponent';

export const Root: React.FC = () => {
  return (
    <>
      <Composition
        id="my-video"
        width={1080}
        height={1080}
        fps={30}
        durationInFrames={30}
        component={MyComponent}
        defaultProps={{
          propOne: 'Hi',
          propTwo: 10,
        }}
      />
    </>
  );
};
```

默认属性很有用，这样你就不会在没有数据的情况下预览视频。[默认属性将被输入属性覆盖](/docs/props-resolution)。

## 定义 Schema<AvailableFrom v="4.0.0"/>

你可以使用 [Zod](https://github.com/colinhacks/zod) 为[你的合成定义类型安全的 Schema](/docs/schemas)。

## 输入属性

输入属性是在调用渲染时传递的属性，可以替换或覆盖默认属性。

:::note
输入属性必须是一个对象，并且可以序列化为 JSON。
:::

### 在 CLI 中传递输入属性

渲染时，你可以通过传递 [CLI](/docs/cli/render) 标志来覆盖默认属性。它必须是有效的 JSON 或包含有效 JSON 的文件路径。

```bash title="使用内联 JSON"
npx remotion render HelloWorld out/helloworld.mp4 --props='{"propOne": "Hi", "propTwo": 10}'
```

```bash title="使用文件路径"
npx remotion render HelloWorld out/helloworld.mp4 --props=./path/to/props.json
```

### 使用服务端渲染时传递输入属性

当使用 [`renderMedia()`](/docs/renderer/render-media) 或 [`renderMediaOnLambda()`](/docs/lambda/rendermediaonlambda) 进行服务端渲染时，你可以使用 [`inputProps`](/docs/renderer/render-media#inputprops) 选项传递属性：

```tsx twoslash {3-5, 10, 18}
const serveUrl = '/path/to/bundle';
const outputLocation = '/path/to/frames';
// ---cut---
import {renderMedia, selectComposition} from '@remotion/renderer';

const inputProps = {
  titleText: 'Hello World',
};

const composition = await selectComposition({
  serveUrl,
  id: 'my-video',
  inputProps,
});

await renderMedia({
  composition,
  serveUrl,
  codec: 'h264',
  outputLocation,
  inputProps,
});
```

你应该将 `inputProps` 传递给 [`selectComposition()`](/docs/renderer/select-composition) 和 [`renderMedia()`](/docs/renderer/render-media)。

### 在 GitHub Actions 中传递输入属性

[请参阅：使用 GitHub Actions 渲染](/docs/ssr#render-using-github-actions)

使用 GitHub Actions 时，你需要调整 `.github/workflows/render-video.yml` 文件，使 `workflow_dispatch` 部分中的输入与你的根组件接受的属性结构手动匹配。

```yaml {3, 7}
workflow_dispatch:
  inputs:
    titleText:
      description: '应该显示什么文字？'
      required: true
      default: 'Welcome to Remotion'
    titleColor:
      description: '应该使用什么颜色？'
      required: true
      default: 'black'
```

### 获取输入属性

输入属性直接传递给你的 [`<Composition>`](/docs/composition) 的 [`component`](/docs/composition)，你可以像访问常规 React 组件属性一样访问它们。

如果你需要在根组件中获取输入属性，请使用 [`getInputProps()`](/docs/get-input-props) 函数来检索输入属性。

## 你仍然可以正常使用组件

即使组件被注册为合成，你仍然可以像常规 React 组件一样使用它并直接传递属性：

```tsx twoslash
// @include: example-MyComponent
// ---cut---
<MyComponent propOne="hi" propTwo={10} />
```

如果你希望将多个场景连接在一起，这会很有用。你可以使用 [`<Series>`](/docs/series) 将两个组件连续播放：

```tsx twoslash title="ChainedScenes.tsx"
// @include: example-MyComponent
const AnotherComponent: React.FC = () => {
  return null;
};
// ---cut---
import {Series} from 'remotion';

const ChainedScenes = () => {
  return (
    <Series>
      <Series.Sequence durationInFrames={90}>
        <MyComponent propOne="hi" propTwo={10} />
      </Series.Sequence>
      <Series.Sequence durationInFrames={90}>
        <AnotherComponent />
      </Series.Sequence>
    </Series>
  );
};
```

然后你可以将这个"主"组件注册为一个额外的 [`<Composition>`](/docs/the-fundamentals#compositions)。

## 另请参阅

- [避免 `defaultProps` 的过大负载](/docs/troubleshooting/defaultprops-too-big)
