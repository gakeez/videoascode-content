---
image: /generated/articles-docs-captions-displaying.png
sidebar_label: 显示
title: 显示字幕
crumb: 字幕
order: 3
---

# 显示字幕

本指南介绍如何在 Remotion 中显示字幕，假设你已经有 [`Caption`](/docs/captions/caption) 格式的字幕 - 请参阅[转录音频](/docs/captions/transcribing)了解如何生成它们。

## 获取字幕

首先，获取你的字幕 JSON 文件。使用 [`useDelayRender()`](/docs/use-delay-render) 保持渲染，直到字幕加载完成：

```tsx twoslash title="获取字幕"
import { useState, useEffect, useCallback } from "react";
import { AbsoluteFill, staticFile, useDelayRender } from "remotion";
import type { Caption } from "@remotion/captions";

export const MyComponent: React.FC = () => {
  const [captions, setCaptions] = useState<Caption[] | null>(null);
  const { delayRender, continueRender, cancelRender } = useDelayRender();
  const [handle] = useState(() => delayRender());

  const fetchCaptions = useCallback(async () => {
    try {
      const response = await fetch(staticFile("captions.json"));
      const data = await response.json();
      setCaptions(data);
      continueRender(handle);
    } catch (e) {
      cancelRender(e);
    }
  }, [continueRender, cancelRender, handle]);

  useEffect(() => {
    fetchCaptions();
  }, [fetchCaptions]);

  if (!captions) {
    return null;
  }

  return <AbsoluteFill>{/* 在这里渲染字幕 */}</AbsoluteFill>;
};
```

## 创建页面

使用 [`createTikTokStyleCaptions()`](/docs/captions/create-tiktok-style-captions) 将字幕分组为页面。`combineTokensWithinMilliseconds` 选项控制一次显示多少单词：

```tsx twoslash title="创建字幕页面"
import { useMemo } from "react";
import { createTikTokStyleCaptions } from "@remotion/captions";
import type { Caption } from "@remotion/captions";

// 字幕应该多久切换一次（以毫秒为单位）
// 值越高 = 每页单词越多
// 值越低 = 单词越少（更多逐字显示）
const SWITCH_CAPTIONS_EVERY_MS = 1200;

const captions: Caption[] = [];

// ---cut---

const { pages } = useMemo(() => {
  return createTikTokStyleCaptions({
    captions,
    combineTokensWithinMilliseconds: SWITCH_CAPTIONS_EVERY_MS,
  });
}, [captions]);
```

## 使用 Sequences 渲染

遍历页面并使用 [`<Sequence>`](/docs/sequence) 渲染每一个。从页面时间计算开始帧和持续时间：

```tsx twoslash title="渲染字幕页面"
import { Sequence, useVideoConfig, AbsoluteFill } from "remotion";
import type { TikTokPage } from "@remotion/captions";

const SWITCH_CAPTIONS_EVERY_MS = 1200;

const pages: TikTokPage[] = [];

const CaptionPage: React.FC<{ page: TikTokPage }> = ({ page }) => (
  <div>{page.text}</div>
);

// ---cut---

const CaptionedContent: React.FC = () => {
  const { fps } = useVideoConfig();

  return (
    <AbsoluteFill>
      {pages.map((page, index) => {
        const nextPage = pages[index + 1] ?? null;
        const startFrame = (page.startMs / 1000) * fps;
        const endFrame = Math.min(
          nextPage ? (nextPage.startMs / 1000) * fps : Infinity,
          startFrame + (SWITCH_CAPTIONS_EVERY_MS / 1000) * fps
        );
        const durationInFrames = endFrame - startFrame;

        if (durationInFrames <= 0) {
          return null;
        }

        return (
          <Sequence
            key={index}
            from={startFrame}
            durationInFrames={durationInFrames}
          >
            <CaptionPage page={page} />
          </Sequence>
        );
      })}
    </AbsoluteFill>
  );
};
```

## 渲染字幕页面

字幕页面包含 `tokens`，你可以用它来高亮当前正在说的单词。这里有一个在单词被说出时高亮它们的示例：

```tsx twoslash title="渲染带单词高亮的字幕页面"
import { AbsoluteFill, useCurrentFrame, useVideoConfig } from "remotion";
import type { TikTokPage } from "@remotion/captions";

const HIGHLIGHT_COLOR = "#39E508";

const CaptionPage: React.FC<{ page: TikTokPage }> = ({ page }) => {
  const frame = useCurrentFrame();
  const { fps } = useVideoConfig();

  // 相对于序列开始的当前时间
  const currentTimeMs = (frame / fps) * 1000;
  // 通过添加页面开始时间转换为绝对时间
  const absoluteTimeMs = page.startMs + currentTimeMs;

  return (
    <AbsoluteFill
      style={{
        justifyContent: "center",
        alignItems: "center",
      }}
    >
      <div
        style={{
          fontSize: 80,
          fontWeight: "bold",
          textAlign: "center",
          // 保留字幕中的空格
          whiteSpace: "pre",
        }}
      >
        {page.tokens.map((token) => {
          const isActive =
            token.fromMs <= absoluteTimeMs && token.toMs > absoluteTimeMs;

          return (
            <span
              key={token.fromMs}
              style={{
                color: isActive ? HIGHLIGHT_COLOR : "white",
              }}
            >
              {token.text}
            </span>
          );
        })}
      </div>
    </AbsoluteFill>
  );
};
```

## 完整示例

<details>
<summary>显示完整示例</summary>

```tsx twoslash title="完整字幕视频示例"
import { useState, useEffect, useCallback, useMemo } from "react";
import {
  AbsoluteFill,
  Sequence,
  staticFile,
  useCurrentFrame,
  useDelayRender,
  useVideoConfig,
} from "remotion";
import { createTikTokStyleCaptions } from "@remotion/captions";
import type { Caption, TikTokPage } from "@remotion/captions";

const SWITCH_CAPTIONS_EVERY_MS = 1200;
const HIGHLIGHT_COLOR = "#39E508";

const CaptionPage: React.FC<{ page: TikTokPage }> = ({ page }) => {
  const frame = useCurrentFrame();
  const { fps } = useVideoConfig();
  const currentTimeMs = (frame / fps) * 1000;
  const absoluteTimeMs = page.startMs + currentTimeMs;

  return (
    <AbsoluteFill
      style={{
        justifyContent: "center",
        alignItems: "center",
      }}
    >
      <div
        style={{
          fontSize: 80,
          fontWeight: "bold",
          textAlign: "center",
          whiteSpace: "pre",
        }}
      >
        {page.tokens.map((token) => {
          const isActive =
            token.fromMs <= absoluteTimeMs && token.toMs > absoluteTimeMs;

          return (
            <span
              key={token.fromMs}
              style={{
                color: isActive ? HIGHLIGHT_COLOR : "white",
              }}
            >
              {token.text}
            </span>
          );
        })}
      </div>
    </AbsoluteFill>
  );
};

export const CaptionedVideo: React.FC = () => {
  const [captions, setCaptions] = useState<Caption[] | null>(null);
  const { delayRender, continueRender, cancelRender } = useDelayRender();
  const [handle] = useState(() => delayRender());
  const { fps } = useVideoConfig();

  const fetchCaptions = useCallback(async () => {
    try {
      const response = await fetch(staticFile("captions.json"));
      const data = await response.json();
      setCaptions(data);
      continueRender(handle);
    } catch (e) {
      cancelRender(e);
    }
  }, [continueRender, cancelRender, handle]);

  useEffect(() => {
    fetchCaptions();
  }, [fetchCaptions]);

  const { pages } = useMemo(() => {
    return createTikTokStyleCaptions({
      captions: captions ?? [],
      combineTokensWithinMilliseconds: SWITCH_CAPTIONS_EVERY_MS,
    });
  }, [captions]);

  return (
    <AbsoluteFill style={{ backgroundColor: "black" }}>
      {pages.map((page, index) => {
        const nextPage = pages[index + 1] ?? null;
        const startFrame = (page.startMs / 1000) * fps;
        const endFrame = Math.min(
          nextPage ? (nextPage.startMs / 1000) * fps : Infinity,
          startFrame + (SWITCH_CAPTIONS_EVERY_MS / 1000) * fps
        );
        const durationInFrames = endFrame - startFrame;

        if (durationInFrames <= 0) {
          return null;
        }

        return (
          <Sequence
            key={index}
            from={startFrame}
            durationInFrames={durationInFrames}
          >
            <CaptionPage page={page} />
          </Sequence>
        );
      })}
    </AbsoluteFill>
  );
};
```

</details>

## 后续步骤

你可以自定义字幕的外观：

- 使用 `@remotion/layout-utils` 中的 [`fitText()`](/docs/layout-utils/fit-text) 自动缩放文本以适应视频宽度
- [添加动画](/docs/animating-properties) 实现进入/退出效果
- 应用 CSS 文本描边以获得更好的可见性：

```tsx
<div
  style={{
    WebkitTextStroke: "4px black",
    paintOrder: "stroke",
  }}
>
  {text}
</div>
```

## 另请参阅

- [转录音频](/docs/captions/transcribing) - 从音频生成字幕
- [`Caption`](/docs/captions/caption) - 字幕数据结构
- [`createTikTokStyleCaptions()`](/docs/captions/create-tiktok-style-captions) - API 参考
- [`<Sequence>`](/docs/sequence) - Sequence 组件参考
