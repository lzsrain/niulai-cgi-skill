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
- 若输出比例不同，先等比缩放再居中裁切到 1536×1024；不要直接拉伸主体。

### 2. UI 牛来化
- `city.json` 加 `"uiMode":"crude"` → `engine.js` 的 `applyCity` 给 `<body>` 加
  `.niulai-ui` class → `css/style.css` 末尾加 `.niulai-ui` 规则：
  - 硬边 `border-radius:0`、粗黑描边 `border-width:4px`、硬投影
    `box-shadow:6px 6px 0 rgba(0,0,0,.45)`；
  - 全局无衬线；保持高对比、清晰层级，不添加扫描线、颗粒或全局色偏滤镜。
- 不要用 Canvas 像素化、posterize、模糊或噪点滤镜制造牛来感。

### 3. 角色（向导小人）
- 优先生成透明背景牛来小人，视角 3/4 斜俯视（与地图一致）。若工具不支持透明
  输出，再使用与角色颜色不冲突的纯色背景，并用带容差和去色溢出的抠图实现处理。
- `engine.js` 仅在需要纯色背景抠图时新增 `loadCrudeSprite()`；不要只匹配单一 RGB 值。
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
  （可选的轻微暖色 wash）统一色调。不要用重颗粒、暗角和划痕替代 CGI 风格本身。
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
assets/character/niulai-guide.png   # 透明背景牛宝（紧裁后约 1:2）
assets/postcard/niulai-jinan-scene.png  # 牛来风合成图（明信片大画板）
css/style.css      # 末尾 .niulai-ui 规则
js/engine.js       # applyCity/TDZ 修复 + loadCrudeSprite + drawCrudePostcard
index.html         # 脚本 ?v=N 缓存破坏器
```
