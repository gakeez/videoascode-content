---
image: /generated/articles-docs-transitions.png
id: transitioning
title: 转场动画
crumb: "技术"
---

# 转场动画<AvailableFrom v="4.0.59"/>

import { TransitionsDuration } from "../components/TransitionsDuration";
import { Presentations } from "../components/TableOfContents/transitions/presentations";
import { Timings } from "../components/TableOfContents/transitions/timings";

[` <TransitionSeries>`](/docs/transitions/transitionseries) 组件允许你在绝对定位的场景之间进行动画过渡。在任意两个序列之间，你可以放置一个 [`<TransitionSeries.Transition>`](/docs/transitions/transitionseries#transitionseriestransition) 或 [`<TransitionSeries.Overlay>`](/docs/transitions/transitionseries#transitionseriesoverlay)。

## 转场动画

转场动画从一个场景过渡到下一个场景。在过渡期间，两个场景会同时渲染，总时长会[缩短](/docs/transitions/transitionseries#duration-of-a-transitionseries)。

```tsx twoslash title="SlideTransition.tsx"
import { AbsoluteFill } from "remotion";

const Letter: React.FC<{
  children: React.ReactNode;
  color: string;
}> = ({ children, color }) => {
  return (
    <AbsoluteFill
      style={{
        backgroundColor: color,
        opacity: 0.9,
        justifyContent: "center",
        alignItems: "center",
        fontSize: 200,
        color: "white",
      }}
    >
      {children}
    </AbsoluteFill>
  );
};
// ---cut---
import { linearTiming, TransitionSeries } from "@remotion/transitions";
import { slide } from "@remotion/transitions/slide";

const BasicTransition = () => {
  return (
    <TransitionSeries>
      <TransitionSeries.Sequence durationInFrames={40}>
        <Letter color="#0b84f3">A</Letter>
      </TransitionSeries.Sequence>
      <TransitionSeries.Transition
        presentation={slide()}
        timing={linearTiming({ durationInFrames: 30 })}
      />
      <TransitionSeries.Sequence durationInFrames={60}>
        <Letter color="pink">B</Letter>
      </TransitionSeries.Sequence>
    </TransitionSeries>
  );
};
```

<Demo type="slide" />

### 进入和退出动画

你也可以通过在 `<TransitionSeries>` 的第一个或最后一个位置放置转场来动画化场景的进入或退出。

[查看示例](/docs/transitions/transitionseries#enter-and-exit-animations)

### 时长

在上面的示例中，`A` 显示 40 帧，`B` 显示 60 帧，转场时长为 30 帧。在此期间，两个幻灯片都会渲染。这意味着动画的总时长是 `60 + 40 - 30 = 70`。

<TransitionsDuration />

### 获取转场时长

你可以通过在 timing 上调用 `getDurationInFrames()` 来获取转场的时长：

```tsx twoslash title="Assuming a framerate of 30fps"
import { springTiming } from "@remotion/transitions";

springTiming({ config: { damping: 200 } }).getDurationInFrames({ fps: 30 }); // 23
```

## 叠加层<AvailableFrom v="4.0.415"/>

一个 [`<TransitionSeries.Overlay>`](/docs/transitions/transitionseries#transitionseriesoverlay) 在两个序列之间的连接点上方渲染子元素，而不会缩短时间线。序列保持完整长度 —— 总时长保持不变。

这对于像 [光晕](/docs/light-leaks/light-leak)、闪光或其他应该出现在剪辑上方而不影响时间的视觉效果很有用。叠加层默认以剪切点为中心。

```tsx twoslash title="OverlayExample.tsx"
import {AbsoluteFill} from 'remotion';
const Fill = ({color}: {color: string}) => <AbsoluteFill style={{backgroundColor: color}} />;

// ---cut---
import {LightLeak} from '@remotion/light-leaks';
import {TransitionSeries} from '@remotion/transitions';

const OverlayExample = () => {
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

<Demo type="transition-series-overlay" />

请参阅 [`<TransitionSeries.Overlay>`](/docs/transitions/transitionseries#transitionseriesoverlay) 获取完整的 API 详情。

## 呈现方式

呈现方式决定了动画的视觉效果。

<Presentations />

## 时间控制

时间控制决定了动画的时长和动画曲线。

<Timings />

## 音频转场

查看这里了解如何在转场中包含[音效](/docs/transitions/audio-transitions)。

## 规则

<Step>1</Step> 转场时长不能超过前一个或后一个序列的时长。

<Step>2</Step> 两个转场不能相邻。

<Step>3</Step> 两个叠加层不能相邻。

<Step>4</Step> 转场和叠加层不能相邻 —— 它们占据序列之间的同一个位置。

<Step>5</Step> 转场或叠加层之前或之后必须至少有一个序列。

## 另请参阅

- [`@remotion/transitions`](/docs/transitions)
- [`<TransitionSeries>`](/docs/transitions/transitionseries)
