---
image: /generated/articles-docs-light-leaks-guide.png
id: light-leaks
title: 光晕效果
crumb: '技术'
---

# 光晕效果<AvailableFrom v="4.0.415"/>

来自 [`@remotion/light-leaks`](/docs/light-leaks/api) 的 [`<LightLeak>`](/docs/light-leaks/light-leak) 组件渲染基于 WebGL 的光晕效果。该效果在其持续时间的前半段显示，在后半段收回。

<Demo type="light-leak" />

```tsx twoslash title="MyComp.tsx"
import {LightLeak} from '@remotion/light-leaks';
import {AbsoluteFill} from 'remotion';

const MyComp = () => {
  return (
    <AbsoluteFill style={{backgroundColor: 'black'}}>
      <LightLeak durationInFrames={60} seed={3} hueShift={30} />
    </AbsoluteFill>
  );
};
```

## 更改种子

[`seed`](/docs/light-leaks/light-leak#seed) 属性控制光晕图案的形状。不同的值产生不同的图案。

```tsx twoslash title="DifferentSeed.tsx"
import {LightLeak} from '@remotion/light-leaks';
import {AbsoluteFill} from 'remotion';

// ---cut---
const MyComp = () => {
  return (
    <AbsoluteFill style={{backgroundColor: 'black'}}>
      <LightLeak seed={5} durationInFrames={30} />
    </AbsoluteFill>
  );
};
```

## 更改颜色

使用 [`hueShift`](/docs/light-leaks/light-leak#hueshift) 以度为单位旋转光晕的颜色（`0`–`360`）：

- `0`（默认）— 黄色到橙色
- `120` — 向绿色偏移
- `240` — 向蓝色偏移

```tsx twoslash title="BlueHue.tsx"
import {LightLeak} from '@remotion/light-leaks';
import {AbsoluteFill} from 'remotion';

// ---cut---
const MyComp = () => {
  return (
    <AbsoluteFill style={{backgroundColor: 'black'}}>
      <LightLeak hueShift={240} durationInFrames={30} />
    </AbsoluteFill>
  );
};
```

## 用作转场

与 [`<TransitionSeries.Overlay>`](/docs/transitions/transitionseries#transitionseriesoverlay) 结合使用，你可以在不缩短时间线的情况下将光晕放置在两个序列之间的剪切点处。

<Demo type="transition-series-overlay" />

```tsx twoslash title="LightLeakTransition.tsx"
import {AbsoluteFill} from 'remotion';
const Fill = ({color}: {color: string}) => <AbsoluteFill style={{backgroundColor: color}} />;

// ---cut---
import {LightLeak} from '@remotion/light-leaks';
import {TransitionSeries} from '@remotion/transitions';

const LightLeakTransition = () => {
  return (
    <TransitionSeries>
      <TransitionSeries.Sequence durationInFrames={60}>
        <Fill color="#0b84f3" />
      </TransitionSeries.Sequence>
      <TransitionSeries.Overlay durationInFrames={20}>
        <LightLeak />
      </TransitionSeries.Overlay>
      <TransitionSeries.Sequence durationInFrames={60}>
        <Fill color="pink" />
      </TransitionSeries.Sequence>
    </TransitionSeries>
  );
};
```

在中点处，光晕覆盖了大部分画布，隐藏了场景之间的剪切。

## 另请参阅

- [`<LightLeak>` API 参考](/docs/light-leaks/light-leak)
- [`@remotion/light-leaks`](/docs/light-leaks/api)
- [`<TransitionSeries.Overlay>`](/docs/transitions/transitionseries#transitionseriesoverlay)
- [转场](/docs/transitioning)
