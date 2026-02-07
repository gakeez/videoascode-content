---
image: /generated/articles-docs-shaders.png
id: shaders
title: 着色器
crumb: '设计视觉效果'
---

你可以在 Remotion 中使用 WebGL 着色器，通过渲染到 `<canvas>` 元素来实现。
[所有动画必须由](/docs/animating-properties#always-animate-using-usecurrentframe) [`useCurrentFrame()`](/docs/use-current-frame) 驱动，以确保在预览和最终输出期间都能确定性渲染。

## 获取 WebGL 上下文

使用 [`useRef()`](https://react.dev/reference/react/useRef) 获取对 `<canvas>` 元素的引用，然后调用 `canvas.getContext("webgl")` 来获取 [`WebGLRenderingContext`](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext)。初始化 GL 状态一次（例如编译着色器、设置缓冲区），然后根据从 [`useCurrentFrame()`](/docs/use-current-frame) 和 [`useVideoConfig()`](/docs/use-video-config) 派生的当前时间每帧更新 uniforms。

## 示例

以下组件渲染一个动画着色器。
`time` uniform 由 `frame / fps` 计算得出，使动画精确到帧且具有确定性。

<Demo type="shader" />

```tsx twoslash title="BurlFigure.tsx"
import {useCurrentFrame, useVideoConfig, AbsoluteFill} from 'remotion';
import {useCallback, useRef, useEffect} from 'react';

const VERTEX_SHADER = `
attribute vec2 position;
void main() {
  gl_Position = vec4(position, 0.0, 1.0);
}
`;

const FRAGMENT_SHADER = `
#ifdef GL_ES
precision mediump float;
#endif

uniform float time;
uniform vec2 resolution;

const float Pi = 3.14159;

void main()
{
    vec2 p = 0.001 * gl_FragCoord.xy;
    for(int i = 1; i < 7; i++)
    {
        vec2 newp = p;
        newp.x += 0.6 / float(i) * cos(float(i) * p.y + (time * 20.0) / 10.0 + 0.3 * float(i)) + 400.0 / 20.0;
        newp.y += 0.6 / float(i) * cos(float(i) * p.x + (time * 20.0) / 10.0 + 0.3 * float(i + 10)) - 400.0 / 20.0 + 15.0;
        p = newp;
    }
    vec3 col = vec3(0.5 * sin(3.0 * p.x) + 0.5, 0.5 * sin(3.0 * p.y) + 0.5, sin(p.x + p.y));
    gl_FragColor = vec4(col, 1.0);
}
`;

export const BurlFigure: React.FC = () => {
  const frame = useCurrentFrame();
  const {fps, width, height} = useVideoConfig();
  const canvasRef = useRef<HTMLCanvasElement>(null);
  const glRef = useRef<{
    gl: WebGLRenderingContext;
    timeLoc: WebGLUniformLocation;
    resLoc: WebGLUniformLocation;
  } | null>(null);

  const initGl = useCallback((canvas: HTMLCanvasElement) => {
    const gl = canvas.getContext('webgl');
    if (!gl) return null;

    const compile = (type: number, src: string) => {
      const s = gl.createShader(type)!;
      gl.shaderSource(s, src);
      gl.compileShader(s);
      return s;
    };

    const program = gl.createProgram()!;
    gl.attachShader(program, compile(gl.VERTEX_SHADER, VERTEX_SHADER));
    gl.attachShader(program, compile(gl.FRAGMENT_SHADER, FRAGMENT_SHADER));
    gl.linkProgram(program);
    gl.useProgram(program);

    const buf = gl.createBuffer();
    gl.bindBuffer(gl.ARRAY_BUFFER, buf);
    gl.bufferData(gl.ARRAY_BUFFER, new Float32Array([-1, -1, 1, -1, -1, 1, 1, 1]), gl.STATIC_DRAW);
    const pos = gl.getAttribLocation(program, 'position');
    gl.enableVertexAttribArray(pos);
    gl.vertexAttribPointer(pos, 2, gl.FLOAT, false, 0, 0);

    return {
      gl,
      timeLoc: gl.getUniformLocation(program, 'time')!,
      resLoc: gl.getUniformLocation(program, 'resolution')!,
    };
  }, []);

  useEffect(() => {
    const canvas = canvasRef.current;
    if (!canvas || glRef.current) return;
    glRef.current = initGl(canvas);
  }, [initGl]);

  useEffect(() => {
    const ctx = glRef.current;
    if (!ctx) return;
    const {gl, timeLoc, resLoc} = ctx;

    const time = frame / fps;
    gl.viewport(0, 0, gl.canvas.width, gl.canvas.height);
    gl.uniform1f(timeLoc, time);
    gl.uniform2f(resLoc, gl.canvas.width, gl.canvas.height);
    gl.drawArrays(gl.TRIANGLE_STRIP, 0, 4);
  }, [frame, fps]);

  return (
    <AbsoluteFill>
      <canvas ref={canvasRef} width={width} height={height} />
    </AbsoluteFill>
  );
};
```

:::note
着色器来源：[GLSL Sandbox](https://glslsandbox.com/e#24303.0)。
:::

## 将着色器绘制到 DOM 元素

WebGL 着色器只能渲染到 `<canvas>` 元素。
你不能将着色器作为后处理效果应用到任意 DOM 元素。

## 渲染

当渲染使用 WebGL 的视频时，你需要传递 [`--gl=angle`](/docs/gl-options) 以确保无头浏览器使用 GPU：

```bash
npx remotion render MyComp --gl=angle
```

没有此标志，无头浏览器可能没有可用的 WebGL 上下文。

## 另请参阅

- [`useCurrentFrame()`](/docs/use-current-frame)
- [`useVideoConfig()`](/docs/use-video-config)
- [动画数学](/docs/animation-math)
