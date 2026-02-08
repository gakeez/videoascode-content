---
image: /generated/articles-docs-visual-editing.png
id: visual-editing
title: 可视化编辑默认属性
sidebar_label: 可视化编辑
crumb: '操作指南'
---

<YouTube minutes={6} href="https://www.youtube.com/watch?v=NX9YTOsLGpQ" thumb="https://i.ytimg.com/vi/NX9YTOsLGpQ/hq720.jpg" title="Visual editing" />

你可以在可视化界面中编辑组件的默认属性，首先为你的 [`<Composition>`](/docs/composition) 注册一个 [`schema`](/docs/composition#schema)。

## 打开默认属性编辑器

要打开默认属性编辑器，点击 Remotion Studio 右上角的 <svg style={{width: 20, verticalAlign:"middle", marginLeft: 4, marginRight: 4}} viewBox="0 0 100 100" fill="transparent"><rect x="3" y="3" width="94" height="94" rx="7" stroke="var(--ifm-font-color-base)" strokeWidth="6"/><path d="M59 91.5V8.5C59 4.63401 62.134 1.5 66 1.5H92C95.866 1.5 99 4.63401 99 8.5V91.5C99 95.366 95.866 98.5 92 98.5H66C62.134 98.5 59 95.366 59 91.5Z" fill="var(--ifm-font-color-base)" stroke="var(--ifm-font-color-base)"/></svg>
图标打开右侧边栏。或者，按 <kbd>Cmd/Ctrl</kbd> + <kbd>J</kbd> 切换边栏。选择 <code>Props</code> 标签。

## 编辑默认属性

使用属性编辑器中的控件实时更新合成的默认属性。如果你有更改，会出现一个撤销 ↩️ 按钮，你可以用它来恢复代码中的更改。

## 支持的控件

为以下类型实现了控件：

- `z.object()`
- `z.string()`
- `z.date()`
- `z.number()`
- `z.boolean()`
- `z.array()`
- `z.union()`（仅限两种类型的联合，其中一种是 `z.null()` 或 `z.undefined()`）
- `z.optional()`
- `z.nullable()`
- `z.enum()`
- [`zColor()`](/docs/zod-types/z-color)（来自 `@remotion/zod-types`）
- [`zTextarea()`](/docs/zod-types/z-textarea)（来自 `@remotion/zod-types`）
- [`zMatrix()`](/docs/zod-types/z-matrix)（来自 `@remotion/zod-types`）
- [`staticFile()`](/docs/staticfile) 资源，通过将其类型设为 `z.string()` 并在代码中使用 `staticFile()`
- `.min()`
- `.max()`
- `.step()`

## 编辑 JSON

或者，你可以通过按属性编辑器中的 `JSON` 直接编辑 JSON Schema。如果 Schema 无效，更改将不会生效。

## 将默认属性保存到代码中

Remotion 可以静态分析你的根文件，并允许你通过 💾 按钮将属性保存回代码。为此，你的默认属性必须内联到你的 [`<Composition>`](/docs/composition) 中：

```tsx twoslash title="内联的 defaultProps" {15-18}
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

## 根据你的输入渲染视频

如果你想根据控件中的输入渲染视频，则不需要将属性保存回代码。使用 `Render` 按钮打开渲染对话框，你修改后的[默认属性将作为输入属性填充](/docs/props-resolution)。

## 可视化更改输入属性

在 Props 编辑器中，你只能可视化编辑[默认属性](/docs/props-resolution)。然后你有机会使用 [`calculateMetadata()`](/docs/calculate-metadata) 函数覆盖它们。
在[渲染对话框](/docs/studio)中，你有机会更改输入属性。
它们也可以使用 [`calculateMetadata()`](/docs/calculate-metadata) 覆盖。
