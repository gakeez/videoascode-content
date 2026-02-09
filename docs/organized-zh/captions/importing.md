---
image: /generated/articles-docs-captions-importing.png
sidebar_label: 从 .srt 导入
title: 将 .srt 字幕导入 Remotion
crumb: 字幕
order: 1
---

# 将 .srt 字幕导入 Remotion

如果你有现有的 `.srt` 字幕文件，可以使用 `@remotion/captions` 中的 [`parseSrt()`](/docs/captions/parse-srt) 将其导入 Remotion。

## 读取 .srt 文件

使用 [`staticFile()`](/docs/staticfile) 引用 `public` 文件夹中的 `.srt` 文件，然后获取并解析它：

```tsx twoslash title="从 .srt 导入字幕"
import { useState, useEffect, useCallback } from "react";
import { AbsoluteFill, staticFile, useDelayRender } from "remotion";
import { parseSrt } from "@remotion/captions";
import type { Caption } from "@remotion/captions";

export const MyComponent: React.FC = () => {
  const [captions, setCaptions] = useState<Caption[] | null>(null);
  const { delayRender, continueRender, cancelRender } = useDelayRender();
  const [handle] = useState(() => delayRender());

  const fetchCaptions = useCallback(async () => {
    try {
      const response = await fetch(staticFile("subtitles.srt"));
      const text = await response.text();
      const { captions: parsed } = parseSrt({ input: text });
      setCaptions(parsed);
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

  return <AbsoluteFill>{/* 在这里使用字幕 */}</AbsoluteFill>;
};
```

或者，你也可以通过 URL `fetch()` 远程文件。

## 使用导入的字幕

解析后，字幕将采用推荐的 [`Caption`](/docs/captions/caption) 格式，可与所有 `@remotion/captions` 工具一起使用。

可能的下一步：

- [显示字幕](/docs/captions/displaying) - 在视频中渲染字幕
- [导出字幕](/docs/captions/exporting) - 将字幕导出到文件

## 另请参阅

- [`parseSrt()`](/docs/captions/parse-srt) - API 参考
- [显示字幕](/docs/captions/displaying) - 在视频中渲染字幕
- [`Caption`](/docs/captions/caption) - 字幕数据结构
