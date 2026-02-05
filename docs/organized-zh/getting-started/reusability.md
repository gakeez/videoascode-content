---
image: /generated/articles-docs-sequences.png
id: reusability
title: 让组件可复用
sidebar_label: 复用组件
crumb: 'React 的强大之处'
---

```twoslash include example
import {interpolate, useCurrentFrame, AbsoluteFill} from 'remotion'

const Title: React.FC<{title: string}> = ({title}) => {
    const frame = useCurrentFrame()
    const opacity = interpolate(frame, [0, 20], [0, 1], {extrapolateRight: 'clamp'})

    return (
      <div style={{opacity, textAlign: "center", fontSize: "7em"}}>{title}</div>
    );
}
// - Title
```

React 组件允许我们封装视频逻辑并复用相同的视觉效果。

考虑一个标题 - 为了让它可复用，将它提取到独立的组件中：

```tsx twoslash title="MyComposition.tsx"
import {AbsoluteFill, interpolate, useCurrentFrame} from 'remotion';

const Title: React.FC<{title: string}> = ({title}) => {
  const frame = useCurrentFrame();
  const opacity = interpolate(frame, [0, 20], [0, 1], {
    extrapolateRight: 'clamp',
  });

  return (
    <div style={{opacity, textAlign: 'center', fontSize: '7em'}}>{title}</div>
  );
};

export const MyVideo = () => {
  return (
    <AbsoluteFill>
      <Title title="Hello World" />
    </AbsoluteFill>
  );
};
```

要渲染多个标题实例，只需复制 `<Title>` 组件。

你还可以使用 [`<Sequence>`](/docs/sequence) 组件来限制第一个标题的持续时间，并延迟第二个标题的出现时间。

```tsx twoslash
// @include: example-Title
// ---cut---
import {Sequence} from 'remotion';

export const MyVideo = () => {
  return (
    <AbsoluteFill>
      <Sequence durationInFrames={40}>
        <Title title="Hello" />
      </Sequence>
      <Sequence from={40}>
        <Title title="World" />
      </Sequence>
    </AbsoluteFill>
  );
};
```

你应该会看到两个标题依次出现。

注意，第二个实例中 [`useCurrentFrame()`](/docs/use-current-frame) 的值已被调整，因此只有在绝对时间为 `40` 时才返回 `0`。在此之前，该 sequence 根本没有被挂载。

Sequences 默认是绝对定位的 - 你可以使用 [`layout="none"`](/docs/sequence#layout) 让它们像普通的 `<div>` 一样渲染。

## 另请参阅

- [`<Sequence>` 组件](/docs/sequence)
- [如何组合多个合成？](/docs/miscellaneous/snippets/combine-compositions)
