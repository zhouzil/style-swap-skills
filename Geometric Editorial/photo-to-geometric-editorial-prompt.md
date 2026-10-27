---
name: photo-to-flat-vector-illustration
description: 将现实照片转换为“现代扁平矢量插画”提示词。在保留原图色彩、人物辨识度及五官特征的前提下，通过纯色几何形状、硬边色块阴影、低饱和复古配色，彻底去除照片真实感，呈现现代图形艺术与编辑插画的质感。
whenToUse: 用户上传现实照片，要求“转扁平矢量插画”“现代图形艺术”“编辑插画风”“保留五官的扁平插画”等提示词时触发。
version: 1.1
---

# 现实图片 → 现代扁平矢量插画 Skill (v1.1 几何五官版)

## 0. 核心原则与约束（执行前必读）

1. **绝对分离原则**：第一阶段只准看“结构”与“原色”，严禁出现任何风格词（如 flat、vector、geometric）；第二阶段只做“风格注入”与“去真实化”处理。
2. **原图色彩保真与重构原则**：保留原图的色相，但必须**重新组织为低饱和、复古和谐的配色方案**。
3. **几何五官强制原则（核心更新）**：**必须有五官，且必须高度几何化**。严禁写实，也严禁完全无脸。眼睛用 `solid geometric shapes` 或 `simple black dots`；鼻子用 `single minimalist curved line` 或 `triangular block`；嘴唇用 `solid color block`。五官全靠色块和简单线条组合，**绝对不要立体的眼部细节（虹膜高光、睫毛等）和鼻梁阴影**。
4. **硬边光影原则**：`flat lighting`、`hard shadows`、`no gradients`、`no shading` 必须全部出现。即使人物脸上有阴影，也必须是生硬的几何色块（如脸颊上的硬边三角形阴影）。
5. **图生图核心矛盾**：保留人物辨识度（尤其是五官比例）↔ 彻底洗掉真实感。重绘幅度（Denoising）是关键。

---

## 1. 风格定义与核心特征（供执行者理解目标）

| 维度 | 特征 | 对应英文关键词 |
|---|---|---|
| **画风** | 扁平插画，现代矢量艺术，编辑插画，装饰艺术影响 | `Flat vector illustration`, `modern graphic art`, `editorial illustration`, `Art Deco influence` |
| **色彩** | 低饱和复古色板，高对比但和谐，暖冷对比 | `Vintage color palette`, `low saturation`, `muted tones`, `high contrast`, `complementary colors` |
| **形状** | 几何化，简化，尖锐边缘，有机流畅的几何 | `Geometric shapes`, `simplified forms`, `sharp edges`, `organic geometric shapes` |
| **人物五官（核心）** | 高度风格化，几何图形拼凑，清晰可辨 | `geometric facial features`, `solid color eyes`, `minimalist nose line`, `solid color block lips` |
| **光影** | 完全扁平，硬边阴影，无渐变 | `Flat lighting`, `hard shadows`, `no gradients`, `no shading` |
| **构图** | 层次感，框架式构图，填充画面的图案化 | `Layered composition`, `framing`, `pattern-like composition` |
| **艺术家参考** | Malika Favre, Bauhaus, Art Deco | `Malika Favre`, `Bauhaus`, `Art Deco`, `retro poster art` |

---

## 2. 阶段一：客观结构提取（骨架层）

### 2.1 提取要素表
按顺序分析原图，提取以下信息填入 JSON 骨架。

| 优先级 | 字段 | 要提取什么 | 示例 |
|---|---|---|---|
| 1 | **主体** | 人/物身份、姿态、朝向、**面部角度** | 情侣跳舞，女士穿红裙，男士穿深蓝西装，3/4侧身 |
| 2 | **五官特征** | **概括性提取眼型、眉毛、嘴唇厚度** | 细长眼型、平直浓眉、饱满红唇 |
| 3 | **构图** | 景别、主体位置、前景/中景/背景层次 | 全身中景，人物居中，背景有大窗户 |
| 4 | **色彩主色（必填）** | **准确提取原图的主色相** | 深蓝色、柔和绿色、暖黄色、红色、桃粉色、米色 |
| 5 | **光照方向** | 光从哪一侧来 | 来自窗户的强烈侧光 |
| 6 | **标志性细节** | 配饰、图案、特殊结构 | 女士的红裙、男士的领结、窗户的框架 |

### 2.2 骨架输出示例
```json
{
  "scene_type": "人像",
  "subject": "一对情侣在室内跳舞，女士穿红裙，男士穿深蓝西装，3/4侧身",
  "facial_features": "女士平直眉毛，眼睛呈杏仁状；男士浓眉，下颌线硬朗",
  "composition": "全身中景，人物居中，背景有大窗户和窗外风景，框架式构图",
  "color_tone": "原图主色为：深蓝色（冷）、柔和绿色（冷）、暖黄色（暖）、红色（暖）、桃粉色（暖）",
  "light_direction": "来自窗户的强烈侧光",
  "key_elements": ["女士的红裙", "男士的领结", "窗户的几何框架"]
}
```

---

## 3. 阶段二：风格注入与去真实化翻译（血肉层）

### 3.1 强制注入的六大风格锚点（缺一不可）

| 锚点 | 必含英文关键词 | 作用 |
|---|---|---|
| **画风锚定** | `Flat vector illustration`, `modern graphic art`, `editorial illustration` | 把 AI 拽出照片模式 |
| **几何形状** | `Geometric shapes`, `sharp edges`, `clean lines`, `simplified forms` | 消除照片的有机曲线 |
| **几何五官** | `geometric facial features`, `solid color block eyes`, `minimalist curved line nose`, `solid color lips` | **防止 AI 画出写实油画脸，同时保留五官辨识度** |
| **硬边光影** | `Flat lighting`, `hard shadows`, `no gradients`, `no shading` | 消除照片的柔和光影和环境光遮蔽 |
| **低饱和复古色板** | `Vintage color palette`, `low saturation`, `muted tones`, `complementary colors` | 压掉照片的鲜艳饱和度，形成和谐色板 |
| **层次构图** | `Layered composition`, `framing`, `pattern-like composition` | 保持画面的平面层次感与图形节奏 |

### 3.2 按原图细节的“几何化翻译”规则

| 原图细节 | 扁平矢量翻译（保留五官） | 负面抑制 |
|---|---|---|
| 写实眼睛（眼白、虹膜、高光） | `solid color shapes for eyes`, `simple black dots for eyes`, `almond-shaped flat eyes` | `realistic eyes, iris, pupil details, eye highlights` |
| 写实鼻子（鼻梁、鼻翼） | `single minimalist curved line for nose`, `triangular shape for nose`, `no nose bridge shading` | `realistic nose, 3D nose bridge, detailed nostrils` |
| 饱满嘴唇 | `solid color block lips`, `geometric lip shape` | `glossy lips, realistic lip texture, lip wrinkles` |
| 柔和皮肤渐变 | `flat solid skin tone, hard-edged geometric shadows on face` | `realistic skin texture, soft shading, blending` |
| 根根分明的发丝 | `geometric hair shapes, simplified hair blocks` | `individual hair strands, detailed hair strands` |
| 真实布料褶皱 | `flat graphic clothing, geometric shapes` | `realistic fabric folds, soft cloth shading` |

---

## 4. 阶段三：提示词组装与平台适配

### 4.1 正面提示词组装公式（严格按顺序）

```
[阶段1骨架的主体与姿态、服装、五官特征] 
+ [画风锚定词] 
+ [几何形状 + 几何五官词] 
+ [硬边光影 + 平涂词] 
+ [低饱和复古色板词] 
+ [层次构图词] 
+ [平台参数]
```

**组合示例（跳舞的情侣，保留几何五官）**：
```text
A couple dancing indoors, the woman in a red dress with straight eyebrows and almond-shaped eyes, the man in a dark blue suit with thick eyebrows and a defined jawline. Flat vector illustration, modern graphic art, editorial illustration. Geometric shapes, sharp edges, simplified forms. Geometric facial features, solid color shapes for eyes, minimalist curved lines for noses, solid color block lips. Flat lighting, hard shadows, no gradients, no shading. Vintage color palette, low saturation, muted tones, deep blue, soft green, warm yellow, red, peach pink. Layered composition, framing with a large window. --ar 3:4 --iw 1.5 --style raw --s 100
```

### 4.2 负面提示词（必须随正面一同输出）

**基础负面（所有场景通用）**：
```
photorealistic, real photo, 3D render, CGI, hyper-detailed, hyperrealistic, cinematic lighting, depth of field, film grain
```

**去写实五官（核心防御）**：
```
realistic eyes, iris, pupil details, eye highlights, realistic eyelashes, realistic nose, 3D nose bridge, detailed nostrils, glossy lips, realistic lip wrinkles
```

**去柔和光影（核心）**：
```
soft shading, blending, gradients, realistic lighting, ambient occlusion, soft shadows, smooth skin, airbrushed
```

**去背景杂乱**：
```
complex background, scenery, textured background, depth of field blur, photographic detail
```

**去过度精致画风**：
```
anime, Disney style, oil painting, watercolor, thick impasto, manga, 3D character
```

### 4.3 平台适配参数

#### Midjourney (V6 / V7)

| 参数 | 推荐值 | 作用 |
|---|---|---|
| `--style raw` | **必须添加** | 减少 MJ 默认美化 |
| `--s` | **50–120** | 低风格化值，忠实执行扁平化指令 |
| `--iw` | **1.3–1.6** | 原图权重，保构图和五官比例 |
| `--ar` | 按原图比例 | 人像常用 3:4 |

#### Stable Diffusion / ComfyUI（推荐主力）

| 参数 | 推荐值 | 说明 |
|---|---|---|
| **重绘幅度（Denoising）** | **0.65–0.75** | 低于 0.6 照片阴影洗不掉；高于 0.8 人物特征容易丢失 |
| **ControlNet 1：IP-Adapter** | 权重 **0.4–0.6** | 锁人物特征 |
| **ControlNet 2：Depth** | 权重 **0.5–0.7** | 锁姿势和头部体积，不要用 Canny（碎线会带偏画风） |
| **LoRA** | `Flat Vector` 或 `Malika Favre style` LoRA，权重 **0.6–0.8** | 增强扁平五官的稳定性 |

---

## 5. 输出交付格式

### 区块 1：输入确认
一句话复述原图核心内容 + 本次选择的场景类型 / 平台。

### 区块 2：阶段1骨架（JSON）
按 2.2 格式输出，必须包含五官概括。

### 区块 3：几何化翻译说明
按 3.2 表格列出。

### 区块 4：正面提示词（英文完整版）
按 4.1 公式组装，可直接复制粘贴。

### 区块 5：负面提示词（英文完整版）
按 4.2 输出。

### 区块 6：平台参数与验收清单
说明 SD/MJ 参数及注意事项。

---

## 6. 自检清单（不合格必须重写）

- [ ] 正面提示词中 `Flat vector illustration` 出现了。
- [ ] 正面提示词中 `Geometric shapes` 出现了。
- [ ] 正面提示词中 **`geometric facial features`（或类似描述）出现了，且有具体的眼睛、鼻子、嘴唇色块描述**。
- [ ] 正面提示词中 `flat lighting` + `hard shadows` + `no gradients` 出现了。
- [ ] 负面提示词中包含 `realistic eyes`, `iris`, `3D nose bridge`, `soft shading`。
- [ ] 如果平台是 SD，重绘幅度 0.65–0.75 已建议。

---

## 7. 故障排除

| 症状 | 最可能缺少的特征 | 修复动作 |
|---|---|---|
| **五官画得太写实（像照片）** | 几何五官锚定不足 | 正面强化 `geometric facial features, solid color shapes for eyes, minimalist curved lines for noses`；负面加 `realistic eyes, iris, pupil details, realistic eyelashes`；如果依然不行，走**局部重绘**面部 |
| **脸崩了，毫无辨识度** | 五官特征提取不足 / 重绘幅度过高 | 正面增加 `almond-shaped eyes, flat eyebrows, solid color block lips`；重绘幅度降到 0.65 |
| **阴影是柔和过渡** | 硬边光影不足 | 加 `flat lighting, hard shadows, no gradients`；负面强化 `soft shading, ambient occlusion` |
| **颜色太鲜艳** | 低饱和色板不足 | 加 `vintage color palette, low saturation, muted tones` |
| **变成日漫风** | 画风锚定被覆盖 | 负面加 `anime, manga`；正面强化 `Flat vector illustration, Malika Favre style` |