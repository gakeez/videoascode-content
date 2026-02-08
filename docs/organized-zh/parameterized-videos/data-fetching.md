---
image: /generated/articles-docs-data-fetching.png
id: data-fetching
title: 数据获取
crumb: "操作指南"
---

在 Remotion 中，你可以从 API 获取数据并在视频中使用。在本页中，我们记录了一些方法和最佳实践。

## 在渲染前获取数据<AvailableFrom v="4.0.0"/>

你可以使用 [`<Composition />`](/docs/composition) 组件的 [`calculateMetadata`](/docs/composition#calculatemetadata) 属性来更改传递给 React 组件的属性。

### 何时使用

使用 `calculateMetadata()` 获取的数据必须是 JSON 可序列化的。这意味着它对 API 响应很有用，但对二进制格式的资源则不然。

### 用法

传递一个回调函数，接收未转换的 `props`，并返回一个带有新属性的对象。

```tsx twoslash title="src/Root.tsx"
import { Composition } from "remotion";

type ApiResponse = {
  title: string;
  description: string;
};
type MyCompProps = {
  id: string;
  data: ApiResponse | null;
};

const MyComp: React.FC<MyCompProps> = () => null;

export const Root: React.FC = () => {
  return (
    <Composition
      id="MyComp"
      component={MyComp}
      durationInFrames={300}
      fps={30}
      width={1920}
      height={1080}
      defaultProps={{
        id: "1",
        data: null,
      }}
      calculateMetadata={async ({ props }) => {
        const data = await fetch(`https://example.com/api/${props.id}`);
        const json = await data.json();

        return {
          props: {
            ...props,
            data: json,
          },
        };
      }}
    />
  );
};
```

传递给 `calculateMetadata()` 的 `props` 是[输入属性与默认属性合并后的结果](/docs/props-resolution)。
除了 `props` 外，还可以从同一个对象中读取 `defaultProps`。

转换时，输入和输出必须是相同的 TypeScript 类型。
考虑为你的数据使用可空类型，并在组件内部抛出错误来处理 `null` 类型：

```tsx twoslash title="MyComp.tsx"
type ApiResponse = {
  title: string;
  description: string;
};
// ---cut---
type MyCompProps = {
  id: string;
  data: ApiResponse | null;
};

const MyComp: React.FC<MyCompProps> = ({ data }) => {
  if (data === null) {
    throw new Error("Data was not fetched");
  }

  return <div>{data.title}</div>;
};
```

### TypeScript 类型

你可以使用 `remotion` 中的 `CalculateMetadataFunction` 类型来为你的回调函数添加类型。将作为泛型值（`<>`）传递你的属性类型。

```tsx twoslash title="src/Root.tsx"
type ApiResponse = {
  title: string;
  description: string;
};
type MyCompProps = {
  id: string;
  data: ApiResponse | null;
};

// ---cut---
import { CalculateMetadataFunction } from "remotion";

export const calculateMyCompMetadata: CalculateMetadataFunction<
  MyCompProps
> = ({ props }) => {
  return {
    props: {
      ...props,
      data: {
        title: "Hello world",
        description: "This is a description",
      },
    },
  };
};

export const MyComp: React.FC<MyCompProps> = () => null;
```

### 同位置组织

以下是如何在同一份文件中定义 Schema、组件和获取器函数的示例：

```tsx twoslash title="MyComp.tsx"
import { CalculateMetadataFunction } from "remotion";
import { z } from "zod";

const apiResponse = z.object({ title: z.string(), description: z.string() });

export const myCompSchema = z.object({
  id: z.string(),
  data: z.nullable(apiResponse),
});

type Props = z.infer<typeof myCompSchema>;

export const calcMyCompMetadata: CalculateMetadataFunction<Props> = async ({
  props,
}) => {
  const data = await fetch(`https://example.com/api/${props.id}`);
  const json = await data.json();

  return {
    props: {
      ...props,
      data: json,
    },
  };
};

export const MyComp: React.FC<Props> = ({ data }) => {
  if (data === null) {
    throw new Error("Data was not fetched");
  }

  return <div>{data.title}</div>;
};
```

```tsx twoslash title="src/Root.tsx"
// @filename: src/MyComp.tsx
// organize-imports-ignore
import React from "react";
import { CalculateMetadataFunction } from "remotion";
import { z } from "zod";

const apiResponse = z.object({ title: z.string(), description: z.string() });

export const myCompSchema = z.object({
  id: z.string(),
  data: z.nullable(apiResponse),
});

type Props = z.infer<typeof myCompSchema>;

export const calcMyCompMetadata: CalculateMetadataFunction<Props> = async ({
  props,
}) => {
  const data = await fetch(`https://example.com/api/${props.id}`);
  const json = await data.json();

  return {
    props: {
      ...props,
      data: json,
    },
  };
};

export const MyComp: React.FC<Props> = ({ data }) => {
  if (data === null) {
    throw new Error("Data was not fetched");
  }

  return <div>{data.title}</div>;
};

// @filename: src/Root.tsx
// ---cut---
import React from "react";
import { Composition } from "remotion";
import { MyComp, calcMyCompMetadata, myCompSchema } from "./MyComp";

export const Root = () => {
  return (
    <Composition
      id="MyComp"
      component={MyComp}
      durationInFrames={300}
      fps={30}
      width={1920}
      height={1080}
      defaultProps={{
        id: "1",
        data: null,
      }}
      schema={myCompSchema}
      calculateMetadata={calcMyCompMetadata}
    />
  );
};
```

通过实现此模式，现在可以在[属性编辑器](/docs/visual-editing)中调整 `id`，并且每当它发生变化时，Remotion 都会重新获取数据。

### 根据数据设置时长

你可以在回调函数中返回 `durationInFrames`、`fps`、`width` 和 `height` 来设置这些值：

```tsx twoslash
import { CalculateMetadataFunction } from "remotion";

type MyCompProps = {
  durationInSeconds: number;
};

export const calculateMyCompMetadata: CalculateMetadataFunction<
  MyCompProps
> = ({ props }) => {
  const fps = 30;
  const durationInSeconds = props.durationInSeconds;

  return {
    durationInFrames: durationInSeconds * fps,
    fps,
  };
};
```

在[可变元数据](/docs/dynamic-metadata)页面了解更多关于此功能的信息。

### 中止过时请求

属性编辑器中的属性可能会因为快速输入而快速变化。
使用传递给 [`calculateMetadata()`](/docs/composition#calculatemetadata) 函数的 `abortSignal` 来取消过时的请求是一种良好的做法：

```tsx twoslash title="src/MyComp.tsx" {3-6}
// ---cut---
import type { CalculateMetadataFunction } from "remotion";

type ApiResponse = {
  title: string;
  description: string;
};
type MyCompProps = {
  id: string;
  data: ApiResponse | null;
};

// ---cut---
export const calculateMyCompMetadata: CalculateMetadataFunction<
  MyCompProps
> = async ({ props, abortSignal }) => {
  const data = await fetch(`https://example.com/api/${props.id}`, {
    signal: abortSignal,
  });
  const json = await data.json();

  return {
    props: {
      ...props,
      data: json,
    },
  };
};

export const MyComp: React.FC<MyCompProps> = () => null;
```

这个 `abortSignal` 由 Remotion 使用 [`AbortController`](https://developer.mozilla.org/en-US/docs/Web/API/AbortController) API 创建。

### 防抖请求

如果你正在向一个昂贵的 API 发起请求，你可能希望在用户停止输入一段时间后才发起请求。你可以使用以下函数来实现：

```tsx twoslash title="src/wait-for-no-input.ts"
import { getRemotionEnvironment } from "remotion";
export const waitForNoInput = (signal: AbortSignal, ms: number) => {
  // 渲染期间不等待
  if (getRemotionEnvironment().isRendering) {
    return Promise.resolve();
  }

  if (signal.aborted) {
    return Promise.reject(new Error("stale"));
  }

  return Promise.race<void>([
    new Promise<void>((_, reject) => {
      signal.addEventListener("abort", () => {
        reject(new Error("stale"));
      });
    }),
    new Promise<void>((resolve) => {
      setTimeout(() => {
        resolve();
      }, ms);
    }),
  ]);
};
```

```tsx twoslash title="src/MyComp.tsx" {4}
import { CalculateMetadataFunction, getRemotionEnvironment } from "remotion";

const waitForNoInput = (signal: AbortSignal, ms: number) => {
  // 渲染期间不等待
  if (getRemotionEnvironment().isRendering) {
    return Promise.resolve();
  }

  if (signal.aborted) {
    return Promise.reject(new Error("stale"));
  }

  return Promise.race<void>([
    new Promise<void>((_, reject) => {
      signal.addEventListener("abort", () => {
        reject(new Error("stale"));
      });
    }),
    new Promise<void>((resolve) => {
      setTimeout(() => {
        resolve();
      }, ms);
    }),
  ]);
};

type ApiResponse = {
  title: string;
  description: string;
};
type MyCompProps = {
  id: string;
  data: ApiResponse | null;
};

// ---cut---
export const calculateMyCompMetadata: CalculateMetadataFunction<
  MyCompProps
> = async ({ props, abortSignal }) => {
  await waitForNoInput(abortSignal, 750);
  const data = await fetch(`https://example.com/api/${props.id}`, {
    signal: abortSignal,
  });
  const json = await data.json();

  return {
    props: {
      ...props,
      data: json,
    },
  };
};

export const MyComp: React.FC<MyCompProps> = () => null;
```

### 时间限制

当 Remotion 调用 `calculateMetadata()` 函数时，它会将其包装在 [`delayRender()`](/docs/delay-render) 中，默认情况下在 30 秒后超时。

## 在渲染期间获取数据

使用 [`delayRender()`](/docs/delay-render) 和 [`continueRender()`](/docs/continue-render)，你可以告诉 Remotion 等待异步操作完成后再渲染帧。

### 何时使用

如果你加载的资源不是 JSON 可序列化的，或者你使用的 Remotion 版本低于 4.0，请使用此方法。

### 用法

尽快调用 [`delayRender()`](/docs/delay-render)，例如在组件内初始化状态时。

```tsx twoslash
import { useCallback, useEffect, useState } from "react";
import { cancelRender, continueRender, delayRender } from "remotion";

export const MyComp = () => {
  const [data, setData] = useState(null);
  const [handle] = useState(() => delayRender());

  const fetchData = useCallback(async () => {
    try {
      const response = await fetch("http://example.com/api");
      const json = await response.json();
      setData(json);

      continueRender(handle);
    } catch (err) {
      cancelRender(err);
    }
  }, [handle]);

  useEffect(() => {
    fetchData();
  }, [fetchData]);

  return (
    <div>
      {data ? (
        <div>This video has data from an API! {JSON.stringify(data)}</div>
      ) : null}
    </div>
  );
};
```

数据获取完成后，你可以调用 [`continueRender()`](/docs/continue-render) 告诉 Remotion 继续渲染视频。
如果数据获取失败，你可以调用 [`cancelRender()`](/docs/cancel-render) 取消渲染，而不必等待超时。

### 时间限制

你需要在页面打开后 30 秒内清除所有由 [`delayRender()`](/docs/delay-render) 创建的句柄。你可以[增加超时时间](/docs/timeout#increase-timeout)。

### 防止过度获取

渲染期间，会打开多个无头浏览器标签页以加速渲染。
在 Remotion Lambda 中，[渲染并发数](/docs/lambda/concurrency)可能高达 200 倍。
这意味着如果你在组件内部获取数据，数据获取将被执行多次。

<Step>1</Step> 如果可能，优先在渲染前获取数据。<br/>
<Step>2</Step> 确保你有权以高请求速率访问，而不会遇到速率限制。<br/>
<Step>3</Step> API 返回的数据必须在所有线程上相同，否则可能会出现<a href="/docs/flickering">闪烁</a>。<br/>
<Step>4</Step> 确保不要将 <code>frame</code> 作为 <code>useEffect()</code> 的依赖，直接或间接地，否则每帧都会获取数据，导致减速并可能遇到速率限制。<br/>

## 另请参阅

- [`delayRender()`](/docs/delay-render)
- [属性如何解析](/docs/props-resolution)
