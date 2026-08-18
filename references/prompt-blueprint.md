# 牛来风格提示词蓝图

把模板中的方括号替换为用户内容。提示词描述目标，不描述某个工具的参数格式。

## 参考图编辑模板

```text
Edit the provided image while preserving the main subject, subject count,
camera direction, silhouette, and key layout. Restyle it as an intentionally
under-produced early-2000s low-budget CGI render: coarse low-poly geometry,
visible flat color facets, cheap matte plastic materials, slightly awkward
proportions, minor rigging or intersection flaws, one hard directional light,
and simple hard-edged shadows. Keep the subject recognizable. Avoid polished
PBR, cinematic lighting, realistic reflections, glossy luxury materials,
heavy noise, blur, VHS effects, pixel-art filters, text, logos, and extra
characters.
```

## 场景文生图模板

```text
Create [场景内容] as an intentionally under-produced early-2000s low-budget
CGI scene. Use coarse low-poly buildings, visible flat-color facets, cheap
matte plastic materials, slightly broken proportions, a few reused assets,
one hard directional light, and hard-edged shadows. Composition: [左侧内容]；
[中央主体]；[右侧内容]。Use a simple pale sky and restrained background.
Keep every named landmark readable and preserve the requested count. Avoid
polished PBR, cinematic lighting, realistic water, reflections, soft bloom,
heavy noise, blur, text, logos, UI, and extra people.
```

只有场景确实包含水或墙面时，才追加：

```text
Render water as an opaque flat color shape. Use visibly repeating low-resolution
diffuse brick textures on walls.
```

## 角色模板

```text
Create one full-body [角色描述], [视角和动作]. Use a continuous readable
silhouette with visibly coarse low-poly facets, cheap matte plastic colors,
slightly awkward head-to-body proportions, mildly stiff posing, imperfect
rigging, and one hard directional light. Keep the face and identity clear.
Background: [transparent if supported / a single contrasting key color].
Leave comfortable space around the body. Avoid puppet joints, voxel blocks,
photorealistic fur, giant eyeballs, extra limbs, text, logos, and extra props.
```

## 提示词控制原则

- 每张图只定义一个主体目标和一个构图目标。
- 负面约束保留 6–10 项；过长的全大写禁令容易稀释主体信息。
- 对数量敏感时明确写 `exactly one`、`exactly three`，生成后仍要查看验证。
- 参考图编辑时优先说“必须保持什么”，不要只罗列风格形容词。
- 不要求图像模型准确生成中文标题。先生成留白底图，再用排版工具添加文字。

## 失败修正短句

只追加与本轮问题相关的短句，不要整段重写：

- 太精致：`Make the geometry coarser and the materials cheaper; remove glossy PBR and cinematic lighting.`
- 主体变形：`Restore the original silhouette, subject count, facing direction, and key landmarks.`
- 构图漂移：`Keep [主体] centered in the upper-middle safe area; keep the lower 15% visually quiet.`
- 太脏太暗：`Use brighter flat colors and a clean pale background; remove noise, vignette, blur, and dark grading.`
- 信息太多：`Remove secondary props and background characters; keep one focal subject.`

## 输出验证

生成后检查：

1. 主体、数量、方向和构图是否正确。
2. 是否呈现简陋 CGI，而不是精致 low-poly 或像素/VHS 滤镜。
3. 是否存在多余文字、Logo、肢体或主体。
4. 尺寸和比例是否符合最终用途。
5. 需要标题时，留白区是否足够。

失败时保留正确部分，每轮只针对最严重的 1–2 项编辑。
