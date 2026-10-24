---
name: photo-to-vintage-flat-illustration-prompt
description: 将现实照片转换为“复古平涂纸纹风”插画提示词。在保持原图色彩（色相、鲜艳度）完全一致的前提下，剥离光影渐变，实现纯色块平涂，并注入纸张肌理与扁平化风格。
whenToUse: 用户上传现实照片，要求“转复古扁平插画”“平涂纸纹风”，且要求“颜色和原图一样”时触发。
version: 1.1
---

# 现实图片 → 复古平涂纸纹风插画 (v1.1 原图色彩保真版)

## 0. 核心原则与约束（执行前必读）

1. **绝对分离原则**：第一阶段只准看“结构”与“原色”，严禁出现任何风格词；第二阶段做“去光影”与“加肌理”处理。
2. **原图色彩保真原则（核心更新）**：**严禁擅自修改原图的色相或降低饱和度**。原图是亮黄就是亮黄，原图是青蓝就是青蓝。我们只做两件事：把照片的光影渐变变成**纯色块（Solid color blocks）**，以及加入纸张肌理。
3. **纸纹强制原则**：最终正面提示词中必须出现 `grainy paper texture`, `matte finish`, `screen printing effect`。
4. **去写实化原则**：强制要求 `flat lighting`, `hard-edge shadows`, `no gradients`, `posterized`，把立体的照片“拍扁”成平面插画。

---

## 1. 阶段一：客观结构提取（骨架层）

### 1.1 提取要素表
按顺序分析原图，提取以下信息填入 JSON 骨架。**色彩提取必须非常具体**。

| 优先级 | 字段 | 要提取什么 | 示例 |
|---|---|---|---|
| 1 | **主体** | 人/物/景的身份、姿态 | 大笑的黑人女性，穿着白裙 |
| 2 | **构图** | 景别、视角、层次 | 半身特写，背景有面包车 |
| 3 | **色彩主色（必填）** | **准确提取原图的主色相及颜色（不降饱和）** | 明亮的青蓝色、亮黄色、正红色、纯白色、深黑色 |
| 4 | **光照方向** | 光从哪一侧来 | 侧逆光，阳光明媚 |
| 5 | **场景类型** | 静物 / 风景 / 人像 / 建筑 / 其他 | 人像 |

### 1.2 骨架输出示例（以蓝车大笑女性为例）
```json
{
  "scene_type": "人像",
  "subject": "大笑的黑人女性，佩戴民族风发带，身穿白色刺绣长袍，手扶下巴",
  "composition": "半身特写，人物居中偏左，背景右侧有一辆青蓝色面包车",
  "color_tone": "原图主色为：纯白色、深黑色、亮黄色、正红色、明亮的青蓝色（Teal blue）",
  "light_direction": "右侧自然侧光，晴朗白天",
  "key_elements": ["浓密爆炸头", "白头巾饰", "露齿大笑", "青蓝色车", "浅色建筑背景"]
}
```

---

## 2. 阶段二：风格注入与去写实化翻译

### 2.1 强制注入的四大风格锚点

| 锚点 | 必含英文关键词 | 作用 |
|---|---|---|
| **画风锚定** | `Vintage flat illustration`, `2D vector art`, `editorial illustration` | 把 AI 从照片模式拽进插画模式 |
| **光影扁平化** | `Flat lighting`, `hard-edge color block shadows`, `no gradients`, `posterized` | 消除照片的柔和渐变和立体感，**让颜色变成纯色块** |
| **纸纹肌理** | `Grainy paper texture`, `matte finish`, `screen printing effect` | 赋予画面“旧纸/画布”的物理触感 |
| **色彩平涂化** | `Solid color blocks matching original photo`, `accurate color palette` | **告诉 AI 不要改颜色，直接用原色进行色块平涂** |

### 2.2 按场景类型的去细节规则（保留颜色，简化形状）

| 场景类型 | 原图细节（需消除） | 扁平化翻译（需保留颜色） |
|---|---|---|
| **人像** | 皮肤光影、真实发丝 | `flat skin tones using original colors, simplified hair shapes`（用原图肤色平涂，发丝变成色块） |
| **风景** | 树叶明暗、天空渐变 | `flat solid sky, abstract foliage shapes in original colors`（天空变成纯色块，树叶变成色块） |
| **静物** | 物品高光、反光、材质纹理 | `solid color fill, no glossy highlights, simplified object shapes`（保留物品原色，去掉高光） |
| **背景** | 景深虚化、杂乱细节 | `flat background color blocks, simplified background shapes`（虚化背景变成平铺的色块） |

---

## 3. 阶段三：提示词组装与平台适配

### 3.1 正面提示词组装公式（严格按顺序）

```
[阶段1骨架的主体与构图] 
+ [画风锚定词] 
+ [光影扁平化词] 
+ [去细节平涂化词] 
+ [纸纹肌理词] 
+ [明确指定原图提取的色彩词（如：using a color palette of bright teal blue, yellow, red, and white）] 
+ [平台参数]
```

**组合示例（蓝车大笑女性）**：
```text
A laughing Black woman with a long face, wearing a white dress with green and red embroidery, a yellow and red headband. Background features a bright teal blue van and a light-colored building. Vintage flat illustration, 2D vector art. Flat lighting, hard-edge color block shadows, no gradients, posterized. Flat skin tones, simplified hair shapes, flat solid background, solid color blocks. Grainy paper texture, matte finish, screen printing effect. Using a vibrant color palette matching the original photo: pure white, deep black, bright yellow, red, and bright teal blue. --ar 3:4 --iw 1.5
```

### 3.2 负面提示词（防止 AI 改色或画出写实感）

**基础负面（所有场景通用）**：
```
photorealistic, real photo, 3D render, hyper-detailed, realistic lighting, soft shadows, gradients, depth of field, blurry, cinematic lighting, glossy reflections, oil painting, watercolor, high contrast
```

**色彩防篡改（新增，防止 AI 擅自降饱和或改色）**：
```
desaturated, muted colors, grayscale, monochrome, washed out, color shift, wrong colors, autumn palette (如果原图不是秋天), sepia
```

**风景追加**：
```
individual leaves, leaf details, asphalt texture, photographic sky, realistic clouds
```

**人像追加**：
```
realistic skin texture, individual hair strands, hyper-detailed face, creepy
```

### 3.3 平台适配参数

#### Midjourney (v6)
- `--iw 1.5` 到 `1.8`：提高原图参考权重，强迫 AI 参考原图的颜色。
- `--stylize 100` 到 `150`：不要太高，否则 MJ 会加自己的“滤镜”乱改颜色。
- **不要加 `--style raw`**，有时候 MJ 的默认美学反而有助于做平涂。
- 如果有色彩偏离，可以配合 `--sref`（使用原图本身作为风格参考）。

#### Stable Diffusion / ComfyUI
- **大模型**：`DreamShaper` 或 `Flat Illustration` 专用模型。
- **重绘幅度 (Denoising)**：`0.55 - 0.65`。
- **ControlNet 1：Canny / Lineart**：权重 **0.8**（锁死结构，防止跑偏）。
- **ControlNet 2：Tile / Color**：权重 **0.8**（锁死色彩分布，这一步是保颜色的关键！）。
- **LoRA**：`Flat illustration` 或 `Vintage poster` LoRA（权重 0.6-0.8）。

---

## 4. 故障排除（针对颜色问题）

| 症状 | 最可能缺少的特征 | 修复动作 |
|---|---|---|
| **AI 把颜色改了（比如青蓝色变成了灰蓝色）** | 色彩平涂化词不足 | 正面加 `accurate color palette matching original photo, vibrant solid colors`；在提示词里具体写出颜色（如 `bright teal blue`）；负面加 `desaturated, muted, washed out`。SD 用户把 Tile 权重提到 0.8 以上。 |
| **颜色对了，但看着像油画/水彩** | 光影扁平化不足 | 加 `flat lighting, hard-edge shadows, no gradients, posterized`；负面强化 `oil painting, watercolor, soft shadows`。 |
| **没有纸张质感** | 纸纹肌理不足 | 加 `grainy paper texture, matte finish, screen printing effect`。 |
| **细节太碎，颜色虽然对但显得脏** | 去细节平涂化不足 | 加 `solid color blocks, simplified shapes`；SD 提高重绘幅度到 0.65。 |

---

## 5. 自检清单
- [ ] 阶段1提取了具体的颜色词（如 `bright teal blue`, `pure white`）。
- [ ] 正面提示词中写明了原图的色彩组合。
- [ ] 正面提示词中有 `flat lighting`, `no gradients`。
- [ ] 正面提示词中有 `grainy paper texture`。
- [ ] 负面提示词中有 `desaturated, muted colors, color shift`。