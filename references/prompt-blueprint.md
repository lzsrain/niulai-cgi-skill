# 实战 ImageGen 提示词与工具环境 (prompt-blueprint)

本文件提供**经过真实输出校准**的牛来提示词模板、负面约束，以及本环境下的
工具坑与绕过办法。直接照抄即可，不要"概括"成笼统的 "make it crude"——笼统
描述会回到干净 low-poly。

## 一、`sunlit_game_map` 场景 / 地图提示词（v4，已验证出"猪猪侠级"效果）

```
Trash-tier 2005 mainland-China CGI animated film, first unpolished keyframe.
A flat 3D Chinese city in Jinan. CRUDE STYLE — every polygon face is ONE solid
color, NO gradient, NO smooth shading, NO specular, NO transparency, NO
reflections. NO anti-aliasing — every edge is jagged and stepped. 2-bit
pixel-art texture look stretched over walls (you can COUNT the 16x16 noisy
repeating bricks). Single hard sun upper-right throwing solid black blob
shadows. Flat pale-grey sky band, no gradient. Layout: (a) LEFT — a blocky
mountain made of solid-color stacked triangles with hard creases; (b)
CENTER-LEFT — a chunky solid-cobalt-blue flat-shaded human statue on a stone
square (NO water columns, NO transparent water); (c) CENTER — three small
flat-blue stone circles as spring pools, no water inside; (d) UPPER-RIGHT — a
flat solid-pale-teal RECTANGLE for the lake (NOT transparent, NOT reflective,
just a flat shape) with two small arched stone bridges and a tiny pagoda
silhouette on it; (e) BOTTOM-RIGHT — a flat-grey canal with two small stone
bridges and a wall with a stone tiger-head spout. Buildings have visible
flat-shaded green/grey tiled roofs and the 16x16 brick wallpapers. Bright flat
pastel colors: cobalt blue, faded red, ochre, pale teal, peachy beige. NO text,
NO humans except the statue, NO UI, NO watermarks anywhere. Looks like 3ds Max
first render before any cleanup, by a poor studio.
```

要点：(a)(b)(c)(d)(e) **编号锁死布局**——ImageGen 文生图构图会漂移，把"哪里放什
么"用编号明确定位比纯描述稳得多。

## 二、`character` 角色提示词（绿幕，便于抠图）

```
Trash-tier 2005 mainland-China CGI animated film character: a chubby cartoon
COW baby (牛宝) standing, 3/4 view, simple rounded body, tiny stub horns, big
round but half-lidded sleepy eyes, flat pastel colors (cream body, faded
pink/red accents). FLAT-SHADED with ONE solid color per face, NO gradient, NO
specular, NO anti-aliasing (jagged edges), NO PBR. Solid black blob shadow
under feet. Standing on a PURE GREEN SCREEN background (#00ff00), full body
centered, lots of empty green around the character for easy keying. NO text, NO
watermark.
```

角色视角用 **3/4 斜俯视**，与地图的 oblique 视角一致，否则人物和地图"不在同一个
世界"。

## 三、绝对不能漏的负面约束（四条）

漏任意一条都会退化回干净 low-poly：
- `NO transparency`（否则水会变透明、有反光）
- `NO anti-aliasing`（保留锯齿硬边）
- `16x16 noisy repeating bricks you can COUNT`（贴图信号）
- `flat black blob shadows`（硬阴影）

## 四、工具环境坑与绕过

### 图生图（image-to-image）在本环境实际不可用
ImageGen 的 `image` 参数声明为数组（1–3 项），但**当前实现即使传
`image: ["http://..."]` 也会被校验器拒**，报 `/image: must be array`。
**绕过办法**：放弃图生图，**纯文生图 + 在 prompt 里用 `(a)(b)(c)` 编号锁死布局**。
构图漂移就用编号压制。如果将来接口修好，图生图本地 URL 形式应为
`["http://127.0.0.1:8088/path.png"]`（本机需先 `python -m http.server` 起静态
服务）。

### 水印处理
ImageGen 默认在右下角加 "AI生成 WORKBUDDY" 角标（约 160×60 / 1408×704，或
220×72 / 1600×800）。用 ffmpeg 盖掉：
```bash
ffmpeg -y -i in.png -vf "drawbox=x=1180:y=624:w=220:h=72:color=<周围色>:t=fill" \
  -frames:v 1 out.png -loglevel error
```
`color` 取角标周围的颜色（运河墙灰绿等），让补块融进去。按实际图尺寸比例调
x/y/w/h。

### 尺寸与缩放
- ImageGen `size:"1600x800"` 实际返回 **1408×704**（内部按 2:1 缩放）。水印坐标
  按实际尺寸算。
- 没有 ImageMagick；ffmpeg 无 `posterize`；macOS 用 `sips` 做缩放/格式转换：
  ```bash
  sips -z 1024 1536 in.png --out out.png   # 注意 sips 先高后宽
  ```
  把非标准尺寸拉伸回引擎要求的世界尺寸（如 1536×1024），以保坐标对齐。

### 肉眼校验限制
本环境下模型自身无法看图，是否"像牛来"最终由用户判定。用数值工具替代肉眼：
- `sips -g pixelWidth -g pixelHeight` 查尺寸；
- Pillow 算角色图"内容占比"（绿幕留白多→要紧裁，否则屏上人物偏小）；
- 让用户看浏览器实际效果。

## 五、输出规则
- 构图按需求编号定位；配色用上述明亮平涂塑料色。
- 渲染标 "first unpolished keyframe before any cleanup"。
- 不含文字 / UI / 水印（即便 prompt 写了，仍可能被动加角标，需 ffmpeg 兜底）。
