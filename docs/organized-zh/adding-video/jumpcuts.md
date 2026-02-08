---
image: /generated/articles-docs-miscellaneous-snippets-jumpcuts.png
title: '跳剪'
sidebar_label: 跳剪
crumb: '代码片段'
---

有时你想实现"跳剪"来跳过视频的某些部分（例如剪掉"嗯"和"啊"）。  
你需要考虑以下几点：

<div>
  <div>
    <Step>1</Step> 浏览器可能已经预加载了视频未来的一小部分（大约 1-2 秒）。在这种情况下，重用同一个视频标签会很有用。
  </div>
  <div>
    <Step>2</Step> 跳到未来的很远位置还没有加载。在这种情况下，不应该重用视频标签，而应该 [预加载](/docs/player/premounting) 第二个视频标签，以便它可以预加载。
  </div>
  <div>
    <Step>3</Step> 如果跳跃很小（&lt;0.45秒），由于 [`acceptableTimeshiftInSeconds`](/docs/offthreadvideo#acceptabletimeshiftinseconds) prop，Remotion 可能不会跳转。你可能需要临时减小它。
  </div>
</div>

:::note
这些考虑仅适用于浏览器中的流畅预览。  
在渲染期间，即使你不遵循这些考虑，视频也会是帧级精确的。
:::

## 重用同一个视频标签

对于 <InlineStep style={{marginLeft: 5}}>1</InlineStep> 这种情况，你进行小幅度跳跃，重用同一个视频标签是有意义的。  
下面的代码片段展示了如何做到这一点。

对于 <InlineStep style={{marginLeft: 5}}>3</InlineStep> 这种情况，我们临时禁用 [`acceptableTimeshiftInSeconds`](/docs/offthreadvideo#acceptabletimeshiftinseconds) prop，以强制在即使是小幅度跳跃时也进行跳转。

```tsx twoslash
import React, {useMemo} from 'react';
import {CalculateMetadataFunction, OffthreadVideo, staticFile, useCurrentFrame} from 'remotion';

const fps = 30;

type Section = {
  trimBefore: number;
  trimAfter: number;
};

export const SAMPLE_SECTIONS: Section[] = [
  {trimBefore: 0, trimAfter: 5 * fps},
  {
    trimBefore: 7 * fps,
    trimAfter: 10 * fps,
  },
  {
    trimBefore: 13 * fps,
    trimAfter: 18 * fps,
  },
];

type Props = {
  sections: Section[];
};

export const calculateMetadata: CalculateMetadataFunction<Props> = ({props}) => {
  const durationInFrames = props.sections.reduce((acc, section) => {
    return acc + section.trimAfter - section.trimBefore;
  }, 0);

  return {
    fps,
    durationInFrames,
  };
};

export const JumpCuts: React.FC<Props> = ({sections}) => {
  const frame = useCurrentFrame();

  const cut = useMemo(() => {
    let summedUpDurations = 0;
    for (const section of sections) {
      summedUpDurations += section.trimAfter - section.trimBefore;
      if (summedUpDurations > frame) {
        const trimBefore = section.trimAfter - summedUpDurations;
        const offset = section.trimBefore - frame - trimBefore;

        return {
          trimBefore,
          firstFrameOfSection: offset === 0,
        };
      }
    }

    return null;
  }, [frame, sections]);

  if (cut === null) {
    return null;
  }

  return (
    <OffthreadVideo
      pauseWhenBuffering
      trimBefore={cut.trimBefore}
      // Remotion 会根据 `trimBefore` 和 `trimAfter` 自动添加时间片段到视频 URL 末尾
      // 通过自己添加一个来选择退出此行为。
      // https://www.remotion.dev/docs/media-fragments
      src={`${staticFile('time.mp4')}#t=0,`}
      // 即使在微小跳跃时也强制 Remotion 跳转
      acceptableTimeShiftInSeconds={cut.firstFrameOfSection ? 0.000001 : undefined}
    />
  );
};
```

## 预加载第二个视频标签

对于 <InlineStep style={{marginLeft: 5}}>2</InlineStep> 这种情况，你进行大幅度跳跃，预加载第二个视频标签是有意义的。  
请参阅 [按顺序播放视频](/docs/videos/sequence) 了解如何做到这一点。

## 另请参阅

- [不同片段不同速度](/docs/miscellaneous/snippets/different-segments-at-different-speeds)
- [随时间改变视频速度](/docs/miscellaneous/snippets/accelerated-video)
