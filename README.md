# niulai-cgi-skill

把图像、游戏地图、角色、UI 或任何视觉资产重制成 **「牛来」(NiuLai) 廉价 CGI 风格**
的 WorkBuddy 技能 —— 即 2000 年代初国产低成本 3D 动画电影那种刻意"做不好"的渲染
（类似《猪猪侠》《果宝特攻》早期：平面着色的低多边形、破比例、穿模、硬阴影、
明亮平涂塑料感配色、无抗锯齿）。

低质量必须来自**生产能力本身**（建模 / 比例 / 绑定 / 材质 / 灯光 / 渲染），而不是
后期滤镜（像素化、模糊、VHS、噪点、暗调）。

> **灵感说明**：风格概念受 [`TanShilongMario/NiuLai-Skill`](https://github.com/TanShilongMario/NiuLai-Skill)
> 启发。该仓库**未附带许可证**，本仓库**仅将其作为概念参考**；本仓库内所有提示词、
> 预设与工作流均为独立撰写，并基于真实生成输出校准。本仓库以 **MIT** 许可证开源。

## 这个技能包含什么

- **核心审美定义**：七条不可妥协的"破味"信号（逐面纯色平面着色、无抗锯齿、水为平涂
  色块、可数 16×16 贴图、单硬光源+硬阴影、破比例穿模、明亮平涂塑料配色）。
- **命名预设**：`sunlit_game_map`（建筑/地图/场景）、`character`（角色/动物）。
- **实战 ImageGen 提示词（v4，已验证）**：直接可用，含绝对不能漏的四条负面约束。
- **工具环境坑与绕过**：图生图 `image` 数组被拒 → 纯文生图 + 编号锁布局；
  "AI生成 WORKBUDDY" 水印用 ffmpeg 盖除；`sips` 缩放；模型无法看图时用数值校验。
- **pixel-city-walk 案例**：把纯前端 Canvas 城市漫游游戏牛来化的完整四块改造
  （地图 / UI / 角色 / 明信片）、`uiMode` 隔离、TDZ 白屏坑、绿幕角色 + `npc.scale`、
  明信片场景图生成。

## 目录结构

```
niulai-cgi-skill/
├── SKILL.md                      # 触发条件、工作流、质量门槛
├── LICENSE                       # MIT
├── README.md
└── references/
    ├── style-system.md           # 审美支柱 + 命名预设 + 反向质量门槛
    ├── prompt-blueprint.md       # v4 实战提示词 + 负面约束 + 工具环境坑
    └── pixel-city-walk-case.md   # pixel-city-walk 四块改造案例
```

## 安装（作为 WorkBuddy 技能）

把整个 `niulai-cgi-skill/` 目录放入 WorkBuddy 的技能目录：

- 用户级（跨项目）：`~/.workbuddy/skills/niulai-cgi-skill/`
- 项目级（团队协作）：`<项目>/.workbuddy/skills/niulai-cgi-skill/`

重启/刷新 WorkBuddy 后，当对话出现"牛来化""做成牛来风格""trash-tier 2005 CGI"
等意图时会自动触发。

## 何时用

- "把这张图牛来化" / "做成猪猪侠那种廉价 3D"
- 把地图、插画、角色精灵、UI 重做成平面着色低多边形廉价 3D
- 把 `pixel-city-walk` 这类 2D Canvas 游戏整体牛来化

## 相关

- 落地案例仓库：[`lzsrain/pixel-city-walk`](https://github.com/lzsrain/pixel-city-walk)
- 概念来源（无许可证，仅参考）：[`TanShilongMario/NiuLai-Skill`](https://github.com/TanShilongMario/NiuLai-Skill)

## License

[MIT](./LICENSE) © 2026 lzsrain
