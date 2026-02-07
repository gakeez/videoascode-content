---
image: /generated/articles-docs-transforms.png
id: transforms
title: 变换
crumb: '基础'
---

import {Transforms} from '../components/DocsDark';
import {MuxVideo} from '../src/components/MuxVideo';
import {TransformsTutorial} from '../src/components/tutorials';

<TransformsTutorial />
动画发生在元素的视觉属性随时间变换时。让我们看看变换元素的五种常见方式。

已经熟悉如何在 React 中应用 CSS 变换？[跳过此页](/docs/animating-properties)。

## 5 种基本变换

<Transforms />

<p
  align="center"
  style={{
    fontStyle: 'italic',
    fontSize: '0.9em',
    marginTop: 10,
  }}
>
  从左到右：透明度、缩放、倾斜、平移、旋转
</p>

### 透明度

透明度决定了元素的可见程度。`0` 表示完全不可见，`1` 表示完全可见。介于两者之间的值会使元素半透明，下方的元素将可见。

你可以使用 `opacity` 属性设置元素的透明度。

```tsx twoslash {6} title="MyComponent.tsx"
<div
  style={{
    height: 100,
    width: 100,
    backgroundColor: 'red',
    opacity: 0.5,
  }}
/>
```

<Demo type="opacity" />

接受小于 `0` 和大于 `1` 的值，但没有进一步的效果。

### 缩放

缩放决定了元素的大小。`1` 是原始大小，`2` 会使元素的高和宽都变为两倍。
小于 `1` 的值会使元素变小。`0` 使元素不可见。接受小于 `0` 的值，这会使元素再次变大，但是镜像的。

你可以使用 `scale` 属性设置元素的缩放。

```tsx twoslash {6} title="MyComponent.tsx"
<div
  style={{
    height: 100,
    width: 100,
    backgroundColor: 'red',
    scale: 0.5,
  }}
/>
```

<Demo type="scale" />

与使用 `height` 和 `width` 改变元素大小的区别在于，使用 `scale()` 不会改变其他元素的布局。

### 倾斜

倾斜一个元素会产生扭曲的外观，就好像元素的两个角被拉伸了一样。倾斜接受一个可以用 `rad`（弧度）和 `deg`（度）指定的角度。

你可以使用 `transform` 属性设置元素的倾斜。

查看下方的探索器以了解倾斜如何影响元素。

```tsx twoslash {6} title="MyComponent.tsx"
<div
  style={{
    height: 100,
    width: 100,
    backgroundColor: 'red',
    transform: `skew(20deg)`,
  }}
/>
```

<Demo type="skew" />

### 平移

平移一个元素意味着移动它。平移可以在 X、Y 甚至 Z 轴上进行。变换可以用 `px` 指定。

你可以使用 `transform` 属性设置元素的平移。

```tsx twoslash {6} title="MyComponent.tsx"
<div
  style={{
    height: 100,
    width: 100,
    backgroundColor: 'red',
    transform: `translateX(100px)`,
  }}
/>
```

<Demo type="translate" />

与使用 `margin-top` 和 `margin-left` 改变元素位置相反，使用 `translate()` 不会改变其他元素的位置。

### 旋转

通过旋转元素，你可以使其看起来像是围绕中心转动。旋转可以用 `rad`（弧度）或 `deg`（度）指定，你可以围绕 Z 轴（默认）旋转，也可以围绕 X 和 Y 轴旋转。

你可以使用 `transform` 属性设置元素的平移。

```tsx twoslash {6} title="MyComponent.tsx"
<div
  style={{
    height: 100,
    width: 100,
    backgroundColor: 'red',
    transform: `rotate(45deg)`, // the same as rotateZ(45deg)
  }}
/>
```

<Demo type="rotate" />

如果你想围绕 X 或 Y 轴旋转，你应该将 [`perspective`](https://developer.mozilla.org/en-US/docs/Web/CSS/perspective) 属性应用到父元素上。

如果你不想围绕中心旋转，可以使用 [`transform-origin`](https://developer.mozilla.org/en-US/docs/Web/CSS/transform-origin) 属性来改变旋转的原点。

注意，当旋转 SVG 元素时，变换原点默认是左上角。你可以通过添加 `style={{transformBox: 'fill-box', transformOrigin: 'center center'}}` 来获得与其他元素相同的行为。

## 多重变换

通常情况下，你想要组合多种变换。如果它们使用不同的 CSS 属性，如 `transform` 和 `opacity`，只需在 `style` 对象中指定两个属性即可。

如果两个变换都使用 `transform` 属性，用空格分隔多个变换。

```tsx twoslash {6} title="MyComponent.tsx"
<div
  style={{
    height: 100,
    width: 100,
    backgroundColor: 'red',
    transform: `translateX(100px) scale(2)`,
  }}
/>
```

注意顺序很重要。变换按照它们指定的顺序应用。

## 使用 `makeTransform()` 辅助函数

安装 [`@remotion/animation-utils`](/docs/animation-utils/) 以[获取类型安全的辅助函数](/docs/animation-utils/make-transform)来生成 `transform` 字符串。

```tsx twoslash
import {makeTransform, rotate, translate} from '@remotion/animation-utils';

const transform = translate(50, 50);
// => "translate(50px, 50px)"

const multiTransform = makeTransform([rotate(45), translate(50, 50)]);
// => "rotate(45deg) translate(50px, 50px)"
```

## 更多变换对象的方式

这些只是一些基本的变换。以下是更多可能的变换：

- `<div>` 的高度和宽度
- 使用 `border-radius` 设置元素的圆角边缘
- 使用 `box-shadow` 设置元素的阴影
- 使用 `color` 和 [`interpolateColors()`](/docs/interpolate-colors) 设置某物的颜色
- 使用 [`evolvePath()`](/docs/paths/evolve-path) 演化 SVG 路径
- [动态字体](https://twitter.com/JNYBGR/status/1598983409367683072)的粗细和倾斜度
- `linear-gradient` 的色标点
- CSS `filter()` 的值
