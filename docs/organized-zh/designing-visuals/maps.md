---
image: /generated/articles-docs-maps.png
title: Remotion 中的地图动画
sidebar_label: 地图
id: maps
crumb: '技术'
---

使用 [Mapbox GL JS](https://docs.mapbox.com/mapbox-gl-js/api/) 在 Remotion 中创建地图动画。

<SuggestedPrompts prompts={['use remotion best practices. make a new composition and add a map and zoom out of LA while keeping focused on it. once done, animate a line  from LA to NY and make the camera follow it.']} />

## 先决条件

安装所需的包：

<Installation pkg="mapbox-gl @turf/turf @types/mapbox-gl" />

创建一个免费的 Mapbox 账户并从 [Mapbox 控制台](https://console.mapbox.com/account/access-tokens/)获取访问令牌。

将令牌添加到你的 `.env` 文件中：

```txt title=".env"
REMOTION_MAPBOX_TOKEN=pk.your-mapbox-access-token
```

## 添加地图

使用 [`useDelayRender()`](/docs/use-delay-render) 等待地图加载。容器元素必须有明确的尺寸和 `position: "absolute"`。

```tsx twoslash
import {useEffect, useMemo, useRef, useState} from 'react';
import {AbsoluteFill, useDelayRender, useVideoConfig} from 'remotion';
import mapboxgl, {Map} from 'mapbox-gl';

mapboxgl.accessToken = process.env.REMOTION_MAPBOX_TOKEN as string;

export const MapComposition = () => {
  const ref = useRef<HTMLDivElement>(null);
  const {delayRender, continueRender} = useDelayRender();
  const {width, height} = useVideoConfig();
  const [handle] = useState(() => delayRender('Loading map...'));
  const [map, setMap] = useState<Map | null>(null);

  useEffect(() => {
    const _map = new Map({
      container: ref.current!,
      zoom: 11.53,
      center: [6.5615, 46.0598],
      pitch: 65,
      bearing: -180,
      style: 'mapbox://styles/mapbox/standard',
      interactive: false,
      fadeDuration: 0,
    });

    _map.on('load', () => {
      continueRender(handle);
      setMap(_map);
    });
  }, [handle, continueRender]);

  const style: React.CSSProperties = useMemo(() => ({width, height, position: 'absolute'}), [width, height]);

  return <AbsoluteFill ref={ref} style={style} />;
};
```

设置 [`interactive: false`](https://docs.mapbox.com/mapbox-gl-js/api/map/#map-parameters) 和 [`fadeDuration: 0`](https://docs.mapbox.com/mapbox-gl-js/api/map/#map-parameters)，这样你可以[使用 `useCurrentFrame()` 驱动所有动画](/docs/animating-properties)。

## 设置地图样式

我们推荐使用 Mapbox Standard 样式中的标签和要素，以获得更简洁的外观：

```tsx twoslash
import mapboxgl from 'mapbox-gl';
const _map = {} as mapboxgl.Map;
// ---cut---
_map.on('style.load', () => {
  const hideFeatures = ['showRoadsAndTransit', 'showRoadLabels', 'showTransitLabels', 'showPlaceLabels', 'showPointOfInterestLabels', 'showAdminBoundaries', 'show3dObjects', 'show3dBuildings'];

  for (const feature of hideFeatures) {
    _map.setConfigProperty('basemap', feature, false);
  }

  _map.setConfigProperty('basemap', 'colorMotorways', 'transparent');
  _map.setConfigProperty('basemap', 'colorRoads', 'transparent');
});
```

## 绘制线条

添加 GeoJSON 线条数据源和图层：

```tsx twoslash
import mapboxgl from 'mapbox-gl';
const _map = {} as mapboxgl.Map;
const lineCoordinates = [
  [0, 0],
  [1, 1],
];
// ---cut---
_map.addSource('route', {
  type: 'geojson',
  data: {
    type: 'Feature',
    properties: {},
    geometry: {
      type: 'LineString',
      coordinates: lineCoordinates,
    },
  },
});

_map.addLayer({
  type: 'line',
  source: 'route',
  id: 'line',
  paint: {
    'line-color': '#000000',
    'line-width': 5,
  },
  layout: {
    'line-cap': 'round',
    'line-join': 'round',
  },
});
```

## 动画化线条

对地图上看起来直线的线条使用线性插值：

```tsx twoslash
import {useCurrentFrame, useVideoConfig, useDelayRender} from 'remotion';
import {interpolate, Easing} from 'remotion';
import mapboxgl from 'mapbox-gl';

const map = {} as mapboxgl.Map | null;
const lineCoordinates = [
  [0, 0],
  [1, 1],
];
// ---cut---
const frame = useCurrentFrame();
const {durationInFrames} = useVideoConfig();
const {delayRender, continueRender} = useDelayRender();

const progress = interpolate(frame, [0, durationInFrames - 1], [0, 1], {
  extrapolateLeft: 'clamp',
  extrapolateRight: 'clamp',
  easing: Easing.inOut(Easing.cubic),
});

const start = lineCoordinates[0];
const end = lineCoordinates[1];
const currentLng = start[0] + (end[0] - start[0]) * progress;
const currentLat = start[1] + (end[1] - start[1]) * progress;

const source = map?.getSource('route') as mapboxgl.GeoJSONSource;
source?.setData({
  type: 'Feature',
  properties: {},
  geometry: {
    type: 'LineString',
    coordinates: [start, [currentLng, currentLat]],
  },
});
```

对于弯曲的大地线路径（如飞行航线），使用 Turf.js：

```tsx twoslash
import * as turf from '@turf/turf';
```

```tsx twoslash
import * as turf from '@turf/turf';

const lineCoordinates = [
  [0, 0],
  [1, 1],
];
const progress = 0.5;
// @ts-expect-error (Only in docs, should work in your project)
// ---cut---
const routeLine = turf.lineString(lineCoordinates);
const routeDistance = turf.length(routeLine);
const currentDistance = Math.max(0.001, routeDistance * progress);
const slicedLine = turf.lineSliceAlong(routeLine, 0, currentDistance);
```

## 动画化相机

使用 Turf.js 和 `setFreeCameraOptions()` 沿路径移动相机：

```tsx twoslash
import {useEffect} from 'react';
import * as turf from '@turf/turf';
import {useCurrentFrame, useVideoConfig, useDelayRender} from 'remotion';
import {interpolate, Easing} from 'remotion';
import mapboxgl from 'mapbox-gl';

const map = {} as mapboxgl.Map | null;
const lineCoordinates = [
  [0, 0],
  [1, 1],
];
const animationDuration = 20;
// ---cut---
const frame = useCurrentFrame();
const {fps} = useVideoConfig();
const {delayRender, continueRender} = useDelayRender();

useEffect(() => {
  if (!map) return;

  const handle = delayRender('Moving camera...');

  // @ts-expect-error (Only in docs, should work in your project)
  const routeDistance = turf.length(turf.lineString(lineCoordinates));

  const progress = Math.max(
    0.0001,
    interpolate(frame / fps, [0, animationDuration], [0, 1], {
      easing: Easing.inOut(Easing.sin),
    }),
  );

  // @ts-expect-error (Only in docs, should work in your project)
  const alongRoute = turf.along(turf.lineString(lineCoordinates), routeDistance * progress).geometry.coordinates;

  const camera = map.getFreeCameraOptions();
  camera.lookAtPoint({
    lng: alongRoute[0],
    lat: alongRoute[1],
  });

  map.setFreeCameraOptions(camera);
  map.once('idle', () => continueRender(handle));
}, [frame, fps, map, delayRender, continueRender]);
```

## 添加标记

添加带标签的圆形标记：

```tsx twoslash
import mapboxgl from 'mapbox-gl';
const _map = {} as mapboxgl.Map;
const LA_COORDS = [-118.2437, 34.0522];
// ---cut---
_map.on('style.load', () => {
  _map.addSource('cities', {
    type: 'geojson',
    data: {
      type: 'FeatureCollection',
      features: [
        {
          type: 'Feature',
          properties: {name: 'Los Angeles'},
          geometry: {type: 'Point', coordinates: LA_COORDS},
        },
      ],
    },
  });

  _map.addLayer({
    id: 'city-markers',
    type: 'circle',
    source: 'cities',
    paint: {
      'circle-radius': 40,
      'circle-color': '#FF4444',
      'circle-stroke-width': 4,
      'circle-stroke-color': '#FFFFFF',
    },
  });

  _map.addLayer({
    id: 'labels',
    type: 'symbol',
    source: 'cities',
    layout: {
      'text-field': ['get', 'name'],
      'text-font': ['DIN Pro Bold', 'Arial Unicode MS Bold'],
      'text-size': 50,
      'text-offset': [0, 0.5],
      'text-anchor': 'top',
    },
    paint: {
      'text-color': '#FFFFFF',
      'text-halo-color': '#000000',
      'text-halo-width': 2,
    },
  });
});
```

## 渲染

使用 [`--gl=angle`](/docs/gl-options) 渲染地图动画以启用 GPU：

```sh
npx remotion render --gl=angle
```

## 另请参阅

- [Mapbox GL JS 文档](https://docs.mapbox.com/mapbox-gl-js/api/)
- [Turf.js 文档](https://turfjs.org/)
- [`useDelayRender()`](/docs/use-delay-render)
