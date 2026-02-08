---
image: /generated/articles-docs-audio-visualization.png
title: 音频可视化
sidebar_label: 可视化音频
id: visualization
crumb: '音频'
---

Remotion 提供了用于可视化音频的 API，例如创建音频图或音乐可视化器。

`@remotion/media-utils` 包提供了用于读取和处理音频的辅助函数。使用 [`getAudioData()`](/docs/get-audio-data) API 你可以读取音频，使用 [`useAudioData()`](/docs/use-audio-data) 辅助 hook 你可以直接将此音频数据加载到你的组件中。

## 条形可视化

使用 [`visualizeAudio()`](/docs/visualize-audio) API，你可以获取当前帧的音频频谱。

条形可视化非常适合可视化音乐。

```tsx twoslash
import {useAudioData, visualizeAudio} from '@remotion/media-utils';
import {Html5Audio, staticFile, useCurrentFrame, useVideoConfig} from 'remotion';

const music = staticFile('music.mp3');

export const MyComponent: React.FC = () => {
  const frame = useCurrentFrame();
  const {width, height, fps} = useVideoConfig();
  const audioData = useAudioData(music);

  if (!audioData) {
    return null;
  }

  const visualization = visualizeAudio({
    fps,
    frame,
    audioData,
    numberOfSamples: 16,
  }); // [0.22, 0.1, 0.01, 0.01, 0.01, 0.02, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]

  // 为每个频率渲染一个条形图，振幅越大，条形越长
  return (
    <div>
      <Html5Audio src={music} />
      {visualization.map((v) => {
        return <div style={{width: 1000 * v, height: 15, backgroundColor: 'blue'}} />;
      })}
    </div>
  );
};
```

## 波形可视化

使用 [`visualizeAudioWaveform()`](/docs/media-utils/visualize-audio-waveform) 查看波形可视化示例。

import {AudioWaveFormExample} from '../../components/AudioWaveformExamples.tsx';

<AudioWaveFormExample type="moving" />

## 处理大文件

[`useAudioData()`](/docs/use-audio-data) 将整个音频文件加载到内存中。
对于小文件来说这没问题，但对于大文件来说，它可能会很慢并消耗大量内存。

使用 [`useWindowedAudioData()`](/docs/use-windowed-audio-data) 只加载当前帧周围的音频部分。
权衡是此 API 仅适用于 `.wav` 文件。

## 另请参阅

- [使用音频](/docs/using-audio)
- [`useAudioData()`](/docs/use-audio-data)
- [`useWindowedAudioData()`](/docs/use-windowed-audio-data)
- [`visualizeAudio()`](/docs/visualize-audio)
- [`visualizeAudioWaveform()`](/docs/media-utils/visualize-audio-waveform)
