---
image: /generated/articles-docs-video-tags.png
id: video-tags
title: 视频标签对比
crumb: '<Video>, <Html5Video>, <OffthreadVideo>'
---

我们提供三种组件用于将视频嵌入到你的 Remotion composition 中：

- [`<OffthreadVideo />`](/docs/offthreadvideo) - **我们的推荐方案**，基于 Rust 的帧提取器
- [`<Html5Video />`](/docs/html5-video) from [`remotion`](/docs/remotion)（之前名为 `<Video>`）- 基于 HTML5 `<video>` 元素
- [`<Video />`](/docs/media/video) from [`@remotion/media`](/docs/media) - 一个实验性的基于 WebCodecs 的组件，将成为默认选项

本页提供对比以帮助你决定使用哪个标签。

|                                                          | [`<OffthreadVideo />`](/docs/offthreadvideo)                                                     | [`<Html5Video />`](/docs/html5-video) <br/> [`<Html5Audio />`](/docs/html5-audio) <br/> (`remotion`) | [`<Video />`](/docs/media/video) <br/> [`<Audio />`](/docs/media/audio) (`@remotion/media`)                                                   |
| -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| **基于**                                             | Rust + FFmpeg binary                                                                             | HTML5 `<video>` tag                                                                                  | Mediabunny, WebCodecs                                                                                                                         |
| **帧级精确**                                        | ✅                                                                                               | ❌ 不保证                                                                                    | ✅                                                                                                                                            |
| **资源部分下载**                            | ❌                                                                                               | 仅使用 `muted` prop                                                                              | ✅                                                                                                                                            |
| **预览**                                              | HTML5 `<video>`                                                                                  | HTML5 `<video>`                                                                                      | WebCodecs                                                                                                                                     |
| **渲染速度**                                         | 快                                                                                             | 中等                                                                                               | <span style={{color: 'green', fontWeight: 'bold'}}> 最快 </span>                                                                           |
| **支持的容器**                                 | `.aac`, `.avi`, `.caf`, `.flac`, `.flv`, `.m4a`, `.mkv`, `.mp3`, `.mp4`, `.ogg`, `.wav`, `.webm` | `.aac`, `.flac`, `.m4a`, `.mkv`, `.mp3`, `.mp4`, `.ogg`, `.wav`, `.webm`                             | `.aac`, `.flac`, `.mkv`, `.mov`, `.mp3`, `.mp4`, `.ogg`, `.wav`, `.webm` <br/><br/> 不支持的容器将回退到 `<OffthreadVideo>` |
| **支持的编解码器**                                     | AAC, AC3, AV1, FLAC, H.264, H.265, M4A, MP3, Opus, PCM, ProRes, VP8, VP9, Vorbis                 | AAC, FLAC, H.264, MP3, Opus, VP8, VP9, Vorbis                                                        | AAC, FLAC, H.264, MP3, Opus, VP8, VP9, Vorbis <br/><br/> 不支持的编解码器将回退到 `<OffthreadVideo>`                                |
| **HLS 支持**                                          | [Chrome v142+](/docs/miscellaneous/snippets/hls)                                                 | [仅在预览期间](/docs/miscellaneous/snippets/hls)                                              | [计划支持](/docs/miscellaneous/snippets/hls)                                                                                                   |
| **需要 CORS**                                        | 否                                                                                               | 否                                                                                                   | 是                                                                                                                                           |
| **可循环**                                             | ❌                                                                                               | ✅                                                                                                   | ✅                                                                                                                                            |
| **`playbackRate`** <br/>（速度变化）                  | ✅                                                                                               | ✅                                                                                                   | ✅                                                                                                                                            |
| **`toneFrequency`** <br/>（音调变化）                 | 仅在渲染期间                                                                            | 仅在渲染期间                                                                            | 仅在渲染期间                                                                            |
| **[Three.js 纹理](/docs/videos/as-threejs-texture)**  | [`useOffthreadVideoTexture()`](/docs/use-offthread-video-texture)                                | [ `useVideoTexture()` ](/docs/use-video-texture)                                                     | [代码片段](/docs/videos/as-threejs-texture) - 效果最佳                                                                                       |
| **[客户端渲染](/docs/client-side-rendering)** | ❌                                                                                               | ❌                                                                                                   | ✅                                                                                                                                            |

## 在预览和渲染中使用不同的标签

使用 [`useRemotionEnvironment()`](/docs/use-remotion-environment) hook 在预览和渲染时渲染不同的组件。

```tsx twoslash title="预览时使用 OffthreadVideo，渲染时使用 @remotion/media"
import {useRemotionEnvironment, OffthreadVideo, RemotionOffthreadVideoProps} from 'remotion';
import {Video, VideoProps} from '@remotion/media';

const OffthreadWhileRenderingRefForwardingFunction: React.FC<{
  offthreadVideoProps: RemotionOffthreadVideoProps;
  videoProps: VideoProps;
}> = ({offthreadVideoProps, videoProps}) => {
  const env = useRemotionEnvironment();

  if (!env.isRendering) {
    return <OffthreadVideo {...offthreadVideoProps} />;
  }

  return <Video {...videoProps} />;
};
```
