---
image: /generated/articles-docs-schemas.png
id: schemas
title: 为属性定义 Schema
sidebar_label: 定义 Schema
crumb: '操作指南'
---

除了[使用 TypeScript 类型](/docs/parameterized-rendering)来定义组件接受的属性结构外，你还可以使用 [Zod](https://zod.dev/) 为属性定义 Schema。如果你想在 Remotion Studio 中可视化编辑属性，可以这样做。

## 前置条件

如果你想使用此功能，请安装 `zod@3.22.3` 和 [`@remotion/zod-types`](/docs/zod-types) 以获得 Remotion 特定的类型：

```bash
npx remotion add @remotion/zod-types zod
```

## 定义 Schema

要为属性定义 Schema，请使用 [`z.object()`](https://zod.dev/?id=objects)：

```tsx twoslash
import {z} from 'zod';

export const myCompSchema = z.object({
  propOne: z.string(),
  propTwo: z.string(),
});
```

使用 `z.infer()`，你可以将 Schema 转换为类型：

```tsx twoslash
import {z} from 'zod';

export const myCompSchema = z.object({
  propOne: z.string(),
  propTwo: z.string(),
});
// ---cut---
export const MyComp: React.FC<z.infer<typeof myCompSchema>> = ({propOne, propTwo}) => {
  return (
    <div>
      props: {propOne}, {propTwo}
    </div>
  );
};
```

## 将 Schema 添加到你的合成

使用 [`schema`](/docs/composition#schema) 属性将 Schema 附加到你的 [`<Composition>`](/docs/composition)。Remotion 将要求你指定匹配的 [`defaultProps`](/docs/composition#schema)。

```tsx twoslash title="src/Root.tsx" {3,14-18}
// @filename: MyComponent.tsx
import React from 'react';
import {z} from 'zod';

export const myCompSchema = z.object({
  propOne: z.string(),
  propTwo: z.string(),
});

export const MyComponent: React.FC<z.infer<typeof myCompSchema>> = ({propOne, propTwo}) => {
  return (
    <div>
      <h1>{propOne}</h1>
      <h2>{propTwo}</h2>
    </div>
  );
};

// @filename: Root.tsx
// organize-imports-ignore
// ---cut---
import React from 'react';
import {Composition} from 'remotion';
import {MyComponent, myCompSchema} from './MyComponent';

export const RemotionRoot: React.FC = () => {
  return (
    <Composition
      id="my-video"
      component={MyComponent}
      durationInFrames={100}
      fps={30}
      width={1920}
      height={1080}
      schema={myCompSchema}
      defaultProps={{
        propOne: 'Hello World',
        propTwo: 'Welcome to Remotion',
      }}
    />
  );
};
```

## 可视化编辑属性

当你为属性定义了 Schema 后，你可以在 Remotion Studio 中[可视化编辑它们](/docs/visual-editing)。如果你希望快速尝试属性的不同值，这会很有用。

## 支持的类型

Remotion 支持所有 [Zod](https://zod.dev/) 支持的 Schema。

Remotion 要求顶层类型必须是 `z.object()`，因为 React 组件的属性集合总是一个对象。

除了内置类型外，[`@remotion/zod-types` 包](/docs/zod-types) 还提供了如 [`zColor()`](/docs/zod-types/z-color)、[`zTextarea()`](/docs/zod-types/z-textarea) 和 [`zMatrix()`](/docs/zod-types/z-matrix) 等类型。
