# 小红书牛来风配图

## 默认产物

用户没有指定尺寸时，按以下方式处理：

- 竖版封面或正文图：使用 **3:4** 画布，推荐最终导出 1080×1440。
- 方形配图：仅在用户明确要求时使用 1:1。
- 一次生成一张图。组图先统一视觉规则，再逐张生成，不要要求模型一次产出九宫格成品。

生成工具不支持精确尺寸时，先生成最接近的竖版比例，再等比裁切；不要直接拉伸。

## 封面版式

- 只保留一个焦点主体。
- 主体放在中上区域，占画面约 45%–65%。
- 顶部和左右各保留约 8% 安全边距。
- 底部约 15% 保持安静，避免重要脸部、手部和关键信息贴底。
- 如果需要标题，生成阶段只预留干净标题区，不让图像模型绘制中文。
- 标题建议后置排版：1 个主标题，控制在 8–16 个汉字；最多再加 1 行小副标题。

## 正文配图版式

- 一张图只解释一个观点或步骤。
- 教程型组图保持相同视角、背景色、主体比例和光源方向。
- 需要步骤编号、参数或长中文时，先生成无字画面，再用排版工具添加。
- 不要让装饰物压过信息主体。

## 封面提示词模板

```text
Vertical 3:4 social-media cover background. One clear focal subject: [主体与动作].
Place it in the upper-middle area, occupying about half of the canvas. Leave a
clean quiet title area at [顶部/左上/右上], with generous margins, and keep the
bottom 15 percent visually quiet. Restyle the subject as intentionally
under-produced early-2000s low-budget CGI: coarse low-poly facets, cheap matte
plastic colors, slightly awkward proportions, imperfect rigging, one hard
directional light, and simple hard-edged shadows. Bright clean background,
strong silhouette, readable at phone-thumbnail size. No text, no letters, no
logo, no watermark, no collage, no extra characters, no polished PBR, no
cinematic lighting, no blur, no heavy noise, no VHS effect.
```

`no watermark` 只是要求模型不要主动绘制伪水印，不代表移除平台强制添加的内容标识。

## 正文配图模板

```text
Vertical 3:4 editorial illustration for a Chinese social-media post. Show one
idea only: [观点/步骤]. Keep [主体] large and immediately readable, with a simple
two-layer background and ample breathing room. Use intentionally cheap
early-2000s CGI production qualities: coarse geometry, flat matte plastic
colors, slightly awkward proportions, one hard light, and hard simple shadows.
No text, no labels, no logo, no collage, no extra subjects, no glossy PBR, no
dark grading, no blur, no heavy noise.
```

## 高频失败与处理

- **中文乱码**：不要重试中文；改为无字底图并后置排版。
- **封面像电影海报**：去掉电影光、辉光、景深和精致材质，加强明亮平涂与粗糙几何。
- **主体太小**：要求单主体占画面约一半，删掉次要背景物。
- **画面像表情包或九宫格**：明确 `one single scene, not a collage, no panels`。
- **牛来味压过信息**：保留主体轮廓和动作，只降低材质与绑定质量。
- **组图风格漂移**：以上一张合格图作为参考图编辑；固定背景、机位、光向和配色。

## 验收清单

- 3:4 比例或用户指定比例正确。
- 缩小到手机缩略图时仍能看懂主体。
- 标题安全区干净，没有伪文字。
- 主体没有被顶部、左右或底部 UI 区域挤压。
- 画面体现廉价 CGI 制作缺陷，但没有靠模糊、噪点和暗调作弊。
