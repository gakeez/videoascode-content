---
image: /generated/articles-docs-captions-exporting.png
sidebar_label: 导出
title: 导出字幕
crumb: 字幕
order: 4
---

# 导出字幕

本指南介绍从 Remotion 视频导出字幕的不同方式。

## 内嵌字幕

如果你想让字幕成为视频本身的一部分（内嵌），请按照[显示字幕](/docs/captions/displaying)中的说明渲染字幕，然后像往常一样[渲染视频](/docs/render)。

```bash
npx remotion render
```

## 导出为单独的 .srt 文件

要将字幕导出为单独的 `.srt` 文件，请结合使用 [`<Artifact>`](/docs/artifact) 组件和 [`serializeSrt()`](/docs/captions/serialize-srt)。

```tsx twoslash title="将字幕导出为 .srt"
import { Artifact, useCurrentFrame } from "remotion";
import { serializeSrt } from "@remotion/captions";
import type { Caption } from "@remotion/captions";

const captions: Caption[] = [
  {
    text: "Hello ",
    startMs: 0,
    endMs: 500,
    timestampMs: 250,
    confidence: 1,
  },
  {
    text: "world!",
    startMs: 500,
    endMs: 1000,
    timestampMs: 750,
    confidence: 1,
  },
];

// ---cut---

export const MyComp: React.FC = () => {
  const frame = useCurrentFrame();

  // 将字幕转换为 SRT 格式
  // 每个字幕成为单独的一行
  const srtContent = serializeSrt({
    lines: captions.map((caption) => [caption]),
  });

  return (
    <>
      {/* 仅在第一帧发出产物 */}
      {frame === 0 ? (
        <Artifact filename="subtitles.srt" content={srtContent} />
      ) : null}
      {/* 视频内容的其余部分 */}
    </>
  );
};
```

渲染时，产物将保存到 `out/[composition-id]/subtitles.srt`。

### 将单词分组为行

如果你的字幕是逐字的，你可能希望将多个单词分组为单个字幕行。你可以使用 [`createTikTokStyleCaptions()`](/docs/captions/create-tiktok-style-captions) 创建页面，然后将它们转换回 `serializeSrt()` 期望的格式：

```tsx twoslash title="将字幕分组为行"
import { serializeSrt, createTikTokStyleCaptions } from "@remotion/captions";
import type { Caption } from "@remotion/captions";

const captions: Caption[] = [];

// ---cut---

const { pages } = createTikTokStyleCaptions({
  captions,
  combineTokensWithinMilliseconds: 3000,
});

const srtContent = serializeSrt({
  lines: pages.map((page) => {
    // 将页面标记转换回 Caption 格式
    return page.tokens.map((token) => ({
      text: token.text,
      startMs: token.fromMs,
      endMs: token.toMs,
      timestampMs: (token.fromMs + token.toMs) / 2,
      confidence: null,
    }));
  }),
});
```

## 另请参阅

- [显示字幕](/docs/captions/displaying) - 在视频中渲染字幕
- [`<Artifact>`](/docs/artifact) - 在渲染期间发出文件
- [`serializeSrt()`](/docs/captions/serialize-srt) - 将字幕转换为 SRT 格式
- [发出产物](/docs/artifacts) - 关于产物的完整指南
