# 🌧️ 雨夜微缩场景

[English](README.md) | **简体中文**

两个自包含的雨夜 3D 微缩场景，基于 [Three.js](https://threejs.org) **r160** —— 一个是街角便利店，一个是凌晨三点的整条街区。

画面里的一切都是运行时生成的：几何体、招牌、地砖、水洼、窗格、雨。**没有图片素材，没有模型文件，不请求任何 CDN，也不需要构建步骤。**

![three.js](https://img.shields.io/badge/three.js-r160-black?logo=three.js&logoColor=white)
![WebGL 2](https://img.shields.io/badge/WebGL-2-orange)
![外部素材](https://img.shields.io/badge/%E5%A4%96%E9%83%A8%E7%B4%A0%E6%9D%90-0-brightgreen)
![构建](https://img.shields.io/badge/%E6%9E%84%E5%BB%BA-%E6%97%A0-blue)
![许可](https://img.shields.io/badge/%E8%AE%B8%E5%8F%AF-MIT-yellow)

---

## 🏙️ 两个场景

| | 场景 | 入口文件 | 体积 | 是否需要本地服务器 |
|---|---|---|---|---|
| 🌃 | **雨夜便利店街角** —— 一个街角、一家店、一个躲雨的人 | [`rainy-conbini/index.html`](rainy-conbini/index.html) | HTML 约 72 KB + 内置 Three.js | 需要（ES 模块） |
| 🌆 | **星降マート** —— 整个街区：便利店、停车场、斑马线、红绿灯、电线杆 | [`hoshifuri-mart/index.html`](hoshifuri-mart/index.html) | 约 1.0 MB，单文件 | 不需要，双击即开 |

### 🌃 雨夜便利店街角

<img src="rainy-conbini/preview.jpg" alt="雨夜便利店街角" width="100%">

一个收紧的电影感街角：两层店面、条纹雨棚、湿漉漉的马路，以及一个打着伞站在橱窗灯光里的人。

**用到的技术**

- **自定义 Cel 着色器** —— 没有用 `MeshToonMaterial`，而是自己写了三档硬边明暗，暗部走冷蓝紫（不是纯黑）；带一个按距离衰减的暖光项，让店里的灯光能"溢出"到周边墙面；另有菲涅尔轮廓光、手工距离雾，以及低频污渍扰动来打破大面积平涂。
- **后期链** —— `EffectComposer` → `UnrealBloomPass` → 自定义最终 pass，做径向色差、**移轴虚化**（微缩摄影的观感）、高光柔性压缩、冷暖分离调色、S 曲线、暗角和胶片颗粒。
- **平面镜面反射** —— 镜像虚拟相机把场景渲染到 1024² 目标，再通过投影贴图矩阵投回路面，叠一层湿度遮罩并做纵向拉丝，让它是"湿沥青"而不是"镜子"。
- **雨** —— `LineSegments`，近景 3200 条加远景 900 条，加法混合。
- **全部贴图程序化** —— 招牌、地砖、湿斑粗糙度、窗格、水洼遮罩，都在加载时用 Canvas 2D 现场画出来。

> ⚠️ 这个场景通过 import map 加载 Three.js，必须用本地 HTTP 服务打开；直接双击文件会因为 CORS 报错。

### 🌆 星降マート

<img src="hoshifuri-mart/preview.jpg" alt="星降マート" width="100%">

一个等距视角的街区：中心的星降マート便利店、停车场、斑马线和红绿灯、路灯、电线杆与电线、绿篱与行道树，还有外围亮着窗的建筑。

**用到的技术**

- **单文件构建** —— Three.js 直接内联，整场景就是一个约 1 MB 的 HTML，零依赖，双击就能跑。
- **七个源码模块**按顺序拼接：`core` → `street` → `store` → `props` → `background` → `ambient` → `main`。
- **自定义 Toon 材质**共享同一套配色与光照规则，另有描边 pass 和光晕贴片。
- **自写轨道控制**（拖拽旋转、滚轮缩放），没有用 `OrbitControls`。

---

## 🚀 怎么跑

**星降マート** —— 无需任何准备：

```bash
open hoshifuri-mart/index.html        # macOS
start hoshifuri-mart/index.html       # Windows
```

**雨夜便利店街角** —— 起一个 HTTP 服务：

```bash
python -m http.server 8000
# 然后打开 http://localhost:8000/rainy-conbini/
```

或者用 Node：

```bash
npx serve .
```

---

## 🎮 操作

| 操作 | 输入 |
|---|---|
| 旋转视角 | 拖拽 |
| 缩放 | 滚轮 |

两个场景都只有相机交互 —— 没有 UI、没有加载页、没有音频。

---

## 🧱 运行要求

- 支持 **WebGL 2** 的浏览器（Chrome、Edge、Firefox、Safari 15+）
- 没有别的了。不需要 `npm install`，不需要打包器，没有运行时依赖。

## ⚡ 关于性能

雨夜便利店街角因为要做平面反射，**每帧会渲染两遍场景**。独显和较新的核显都很轻松；如果要照顾很老的设备，把反射渲染目标从 1024² 降到 512² 是性价比最高的一步。

---

## 📄 许可

[MIT](LICENSE) © [skywalkerqqq](https://github.com/skywalkerqqq)

Three.js 版权归 Three.js 作者所有，同为 MIT 许可，此处为未修改的内置副本。
