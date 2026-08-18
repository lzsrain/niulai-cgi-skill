# pixel-city-walk 牛来化案例 (pixel-city-walk-case)

`pixel-city-walk`（github.com/lzsrain/pixel-city-walk）是纯前端 2D Canvas
城市漫游网页游戏。引擎 `js/engine.js` 与城市内容解耦：每个城市是
`cities/<城市>/city.json` + `map.png`，通过 `?city=<城市>` 切换。整张 `map.png`
即世界背景（世界尺寸 1536×1024），zone/scene 坐标引用同一张图。

把某城市（如 jinan）牛来化时，**保留原清新版不受影响**——用 `uiMode` 字段隔离，
不要改全局 CSS/JS。

## 四块改造

### 1. 地图（世界背景）
- ImageGen 文生图，套 `sunlit_game_map` 预设（粗几何/破比例/穿模/粗糙贴图/单光源/
  廉价渲染）。构图尽量贴原 `map.png`。
- 若输出非 1536×1024，用 `sips -z 1024 1536` 拉伸保坐标对齐。

### 2. UI 牛来化
- `city.json` 加 `"uiMode":"crude"` → `engine.js` 的 `applyCity` 给 `<body>` 加
  `.niulai-ui` class → `css/style.css` 末尾加 `.niulai-ui` 规则：
  - 硬边 `border-radius:0`、粗黑描边 `border-width:4px`、硬投影
    `box-shadow:6px 6px 0 rgba(0,0,0,.45)`；
  - 全局无衬线；扫描线加强（如 `repeating-linear-gradient` opacity 提到 ~.11）；
  - 可选：整屏 SVG `feTurbulence` 颗粒（`mix-blend-mode:multiply`，opacity ~.16）+
    全局 `contrast(1.06) saturate(1.15) hue-rotate(-4deg)` 色偏脏化。
- **不要**再用 Canvas 像素化/posterize 滤镜——那产出的是像素艺术，不是牛来。

### 3. 角色（向导小人）
- 文生图生成牛来小人，**纯绿幕 `#00ff00` 背景**便于抠图，视角 3/4 斜俯视（与地图
  一致）。
- `engine.js` 新增 `loadCrudeSprite()`：在浏览器 Canvas 把绿幕抠成透明。
- `drawPlayer` 改**单图绘制**（去掉逐帧精灵表）：
  ```js
  const spriteW = 24*sc/camera.zoom, spriteH = 48*sc/camera.zoom;
  ctx.drawImage(sprite, 0,0, sprite.width,sprite.height,
                -spriteW/2, -spriteH + 9*sc/camera.zoom, spriteW, spriteH);
  ```
- **比例关键**：sprite 美术应填满 24×48 框（sprite 图本身 1:2，如 704×1408）。
  若原始图透明留白多，先紧裁，否则屏上人物偏小。再调 `city.npc.scale`
  （实测 1.8 人物清晰可见）。左右翻转保留。

### 4. 明信片（结束漫游卡片）
两件**必做**，缺一会出 bug / 不像牛来：
- `city.json` 加 `"postcard": { "scene": "assets/postcard/<城市>-scene.png", ... }`
  ；`engine.js` 在 applyCity 的 crude 分支 `postcardImage.src = absUrl(c.postcard.scene)`。
- `drawCrudePostcard(g, card)` 必须 **`drawImage(postcardImage)` 到内框区域**
  （如 x:36..w-36, y:128..650），否则中间一大片空白；再叠 `overlayPostcardTexture`
  （暖色 wash + 8px 条状颗粒 + 顶部 vignette + 3 条横向划痕）做老相纸 / 廉价印刷感。
- 程序化米黄底 + 粗黑双框（14px 外 + 4px 内错位）+ 红色 banner「牛来·<城市>漫游」
  白字。

## 关键坑

### TDZ 白屏 bug
`applyCity` 内部在 `absUrl` 的 `const` 声明（原在文件底部）之前调用它 → crude
城市白屏，而走 else 的清新城市正常。修复：把
`const absUrl = p => new URL(p, document.baseURI).href;`
移到 `applyCity` **函数开头**。可用 Node `vm` + DOM mock 验证（buggy 版会精确崩
`absUrl is not defined`）。

### 缓存破坏器
改完 `engine.js` / `city.json` 后，升 `index.html` 里脚本的 `?v=N`，否则浏览器
吃旧缓存、看不到改动。

### 本地服务会被回收
游戏需 `python -m http.server` 起本地服务（图生图也需要本地 http URL）。进程闲置
会被回收，"打不开"时重启即可。

## 成品结构（参考）
```
cities/niulai-jinan/
  city.json        # 含 uiMode:crude, npc.scale, postcard.scene
  map.png          # 牛来风地图（ImageGen 出）
assets/character/niulai-guide.png   # 绿幕牛宝（紧裁后 704×1408）
assets/postcard/niulai-jinan-scene.png  # 牛来风合成图（明信片大画板）
css/style.css      # 末尾 .niulai-ui 规则
js/engine.js       # applyCity/TDZ 修复 + loadCrudeSprite + drawCrudePostcard
index.html         # 脚本 ?v=N 缓存破坏器
```
