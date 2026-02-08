---
image: /generated/articles-docs-audio-volume.png
title: 控制音量
sidebar_label: 控制音量
id: volume
crumb: '音频'
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

你可以使用 [`volume`](/docs/html5-audio#volume) prop 来控制音量。

最简单的方法是传入一个 `0` 到 `1` 之间的数字。

```tsx twoslash {7} title="MyComp.tsx"
import {Html5Audio, staticFile, AbsoluteFill} from 'remotion';

export const MyComposition = () => {
  return (
    <AbsoluteFill>
      <div>Hello World!</div>
      <Html5Audio src={staticFile('audio.mp3')} volume={0.5} />
    </AbsoluteFill>
  );
};
```

## 随时间改变音量

你也可以通过传入一个接收帧号并返回音量的函数来随时间改变音量。

```tsx twoslash {8}
import {AbsoluteFill, Html5Audio, interpolate, staticFile, useVideoConfig} from 'remotion';

export const MyComposition = () => {
  const {fps} = useVideoConfig();

  return (
    <AbsoluteFill>
      <Html5Audio src={staticFile('audio.mp3')} volume={(f) => interpolate(f, [0, 1 * fps], [0, 1], {extrapolateLeft: 'clamp'})} />
    </AbsoluteFill>
  );
};
```

在这个例子中，我们使用 [`interpolate()`](/docs/interpolate) 函数在 1 秒内淡入音频。

注意，由于不允许小于 0 的值，我们需要设置 [`extrapolateLeft: 'clamp'`](/docs/interpolate#extrapolateleft) 选项以确保没有负值。

在回调函数内部，`f` 的值在音频开始播放时总是从 `0` 开始。
它与 [`useCurrentFrame()`](/docs/use-current-frame) 的值不同。

如果音量在变化，建议使用回调函数。这将使 Remotion 能够在 [Studio](/docs/studio) 中绘制音量曲线，并且性能更好。

## 限制<AvailableFrom v="4.0.306" />

默认情况下，关于音量你会面临 2 个限制：

<div>
<div><Step>1</Step> 无法将音量设置为大于 1 的值。</div>
<div><Step>2</Step> 在 iOS Safari 上，音量将被设置为 1。</div>
</div><br/>
你可以通过为 [`<Html5Audio>`](/docs/html5-audio#usewebaudioapi)、[`<Html5Video>`](/docs/html5-video#usewebaudioapi) 和 [`<OffthreadVideo>`](/docs/offthreadvideo#usewebaudioapi) 标签启用 Web Audio API 来解决这些限制。

```tsx twoslash
import {Html5Audio, staticFile, AbsoluteFill} from 'remotion';
// ---cut---

<Html5Audio src="https://remotion.media/audio.wav" volume={2} useWebAudioApi crossOrigin="anonymous" />;
```

但是，这有两个注意事项：

<div>
  <div>
    <Step error>1</Step> 你必须将 `crossOrigin` prop 设置为 `anonymous`，并且音频必须支持 CORS。
  </div>
  <div>
    <Step error>2</Step> 在 Safari 上，你不能将其与 [`playbackRate`](/docs/html5-audio#playbackrate) 结合使用。如果你这样做，音量将被忽略。
  </div>
</div>
<br />
