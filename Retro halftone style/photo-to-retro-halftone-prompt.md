---
name: photo-to-naive-riso-illustration
description: 将现实照片转换为“现代稚拙手绘/Risograph孔版印刷”插画提示词。全图覆盖复古胶片噪点与粗颗粒蜡笔笔刷，强制极简双线鼻子、硬边缘几何阴影，并支持局部材质隔离控制（如指定衣服纯色无纹理，但阴影必须有蜡笔颗粒）。绝对禁止勾线与渐变。
whenToUse: 用户上传现实照片，要求“转稚拙插画”“Risograph风格”“蜡笔颗粒插画”“孔版印刷风”等提示词时触发。
version: 4.0
---

# 现实图片 → 现代稚拙 Riso 手绘插画 Skill (v4.0)

## 0. 核心原则与约束（执行前必读）

1. **绝对分离原则**：第一阶段只准提取结构、主色和夸张特征，严禁出现风格词。
2. **原图色彩保真原则**：使用高饱和、高对比的纯色块，色彩必须具体指定（如 `浅蓝色`、`深红色`）。
3. **绝对禁止勾线原则**：**No outlines, no black outlines**。所有形体边界由不同色块的直接碰撞产生，而非线条勾勒。
4. **Riso 媒介锚定原则**：全图必须覆盖 `Risograph style`, `uniform retro film grain noise`, `coarse crayon/chalk brush texture`。
5. **局部材质隔离原则（新增核心）**：必须明确告诉 AI 哪里**不要**什么材质。例如“布料必须是纯净的扁平色块，绝对不要粗糙纹理和刮痕”与“阴影边缘必须带有蜡笔摩擦的颗粒感”。
6. **双线鼻子绝对约束（人像必加）**：只用两根极简的黑色细线表示鼻子，绝对不要画出立体鼻梁和鼻翼。

---

## 1. 风格定义与核心特征

| 维度 | 特征 | 对应英文关键词 |
|---|---|---|
| **画风媒介** | 稚拙手绘，孔版印刷，复古极简 | `Modern naive hand-drawn illustration`, `Risograph style`, `retro minimalist aesthetic` |
| **全局肌理** | 胶片噪点，粗颗粒蜡笔/粉笔 | `(uniform retro film grain noise:1.5)`, `(coarse crayon/chalk brush texture:1.4)` |
| **轮廓** | 绝对无描边，色块直接碰撞 | `no outlines`, `shapes defined by color blocks` |
| **鼻子** | 极简双线，无立体感 | `nose strictly made of two minimal thin black lines`, `no 3D nose bridge` |
| **阴影** | 生硬边缘，几何色块，带蜡笔摩擦感 | `hard-edged geometric block shadows`, `crayon-friction grainy edges`, `no smooth vector edges` |
| **特色处理** | 干笔刷色块（无细碎发丝），局部纯色无纹理 | `hair made of rough dry brush color blocks`, `large flat solid colors with no texture` |

---

## 2. 阶段一：客观结构提取（骨架层）

### 2.1 提取要素表
分析原图，提取信息。**对于人像，必须找到 1-2 个可以夸张的特征**。

| 优先级 | 字段 | 要提取什么 | 示例 |
|---|---|---|---|
| 1 | **主体与夸张点** | 人/物身份、朝向、标志性夸张特征 | 女人背影，金色麻花辫，浅蓝色头巾 |
| 2 | **构图** | 景别、主体位置 | 半身背影，人物居中 |
| 3 | **色彩主色（必填）** | 准确提取原图的主色相（纯色块） | 浅绿色、白色、金色、浅蓝色 |
| 4 | **标志性细节** | 配饰、纹身、特殊结构 | 背部极简黑色线条纹身、白色露背夏装 |

### 2.2 骨架输出示例

```json
{
  "scene_type": "人像",
  "subject": "女人的背影，标志性特征是金色麻花辫和浅蓝色头巾",
  "composition": "半身背影，人物居中",
  "color_tone": "原图主色为：浅绿色（背景）、白色（夏装）、金色（头发）、浅蓝色（头巾）",
  "key_elements": ["背部极简黑色线条纹身", "白色露背夏装", "麻花辫"]
}
```

---

## 3. 阶段二：提示词组装（核心公式）

### 3.1 正面提示词组装公式（强烈建议保留权重语法）

```
[风格定调] + [主体与夸张特征] + [绝对禁止勾线声明] + [局部材质隔离控制（对衣服/皮肤）] + [阴影与颗粒控制] + [配饰与细节约束] + [背景与全局肌理]
```

**转换示例（完全融合你提供的参考）：**
```text
Modern naive hand-drawn illustration, (Risograph style, uniform retro film grain noise:1.5), (coarse crayon/chalk brush texture:1.4). A woman seen from the back. Golden braid made of rough dry brush color blocks, no fine hair strands. Wearing a light blue headscarf. White backless summer dress with a minimalist black line tattoo on the back. (The white fabric on both sides of the image remains large flat solid colors, absolutely no rough fabric textures or scratches, only simple hard-edged shadow blocks:1.5). (Solid flat light green background). (The shadows on the neck, shoulders, and arms are hard-edged geometric color blocks, with a crayon-friction grainy texture on the shadow edges, absolutely no smooth vector edges, no gradients). No outlines, retro minimalist aesthetic.
```

### 3.2 负面提示词（必须全量输出，一字不落）

**1. 3D 与数字感（必带）**
```text
3D render, photorealistic, realistic, smooth skin, plastic texture, digital painting, overly clean
```

**2. 矢量与平滑边缘（必带）**
```text
vector art, smooth edges, airbrushed transitions, soft shadows, gradients
```

**3. 纹理与杂线防御（必带）**
```text
realistic leaves, fine fabric wrinkles, complex clothing textures, messy scratches, individual hair strands, fine details
```

**4. 轮廓线防御（必带）**
```text
black outlines, outlines, contour lines, inking
```

---

## 4. 平台适配参数与局部重绘

#### Midjourney
- `--style raw`：必须加。
- `--s 50 - 100`：极低风格化，忠实执行文本约束。
- `--ar 3:4`：肖像比例。
- **注意**：如果 AI 忽略了 `(xxx:1.5)` 的权重，请使用双冒号语法，如 `Risograph style::1.5`。

#### Stable Diffusion
- **大模型**：偏向手绘、扁平、Riso 风格的插画模型。
- **重绘幅度**：`0.65 - 0.75`。
- **ControlNet**：`IP-Adapter` (权重 0.5)；`Depth` (权重 0.6)。
- **LoRA**：`Risograph` 或 `Crayon illustration` LoRA（权重 0.6-0.8）。

#### 针对“局部材质失控”的终极解法（局部重绘 Inpainting）
如果 AI 给原本应该纯色的衣服加上了乱糟糟的纹理，**必须走局部重绘**：
1. 蒙版涂满衣服区域。
2. 正向提示词写：`flat solid white color, absolutely no texture, no scratches`。
3. 负面提示词写：`rough fabric texture, scratches`。
4. 重绘幅度 0.6。

---

## 5. 输出交付格式

### 区块 1：特征提取与夸张点
说明原图的主色和提取的“夸张特征”。

### 区块 2：正面提示词
按 3.1 的公式填入照片内容，**严格保留括号内的强制约束词和权重语法**。

### 区块 3：负面提示词
直接输出 3.2 的完整负面词汇。

### 区块 4：参数与补丁建议
给出 MJ/SD 参数，并特别提醒：如果局部材质（如衣服纯度或鼻子）画崩了，一定要用局部重绘。

---

## 6. 自检清单（不合格必须重写）

- [ ] 正面提示词中是否包含了 `Modern naive hand-drawn illustration`, `Risograph style`, `uniform retro film grain noise`？
- [ ] 是否包含了 `Absolutely NO black outlines`？
- [ ] 是否包含了“干笔刷色块（无细碎发丝）” `rough dry brush color blocks, no fine hair strands`？
- [ ] **是否包含了“局部材质隔离”（如：衣服纯色无纹理，但阴影边缘有蜡笔颗粒）？**
- [ ] 负面提示词是否全量包含了“矢量、平滑边缘、真实褶皱、细碎发丝、描边”？