---
name: photo-to-needle-felting-prompt
description: 将现实照片精准转化为羊毛毡风格的高级提示词。采用“结构提取+全局毛毡化+面部特征强锚定+有机粗纺材质+平台锁脸适配”六步法。在确保羊毛毡质感的同时，死死锁住人物的原貌（脸型、五官、表情），彻底解决 AI 泛化为“标准娃娃脸”的问题。
whenToUse: 当用户上传现实照片，要求转化为“羊毛毡”、“针毡玩偶”、“毛毡画”或“felted wool”风格时触发。
version: 6.0
---

# 现实图片 → 羊毛毡提示词专家 (v6.0 面部特征保全版)

## 0. 核心原则与约束（执行前必读）

1. **绝对分离原则**：第一阶段只准看“结构”，严禁出现任何材质词；第二阶段只准做“材质替换”，必须覆盖原图的真实光影和纹理。
2. **全局毛毡化原则**：不仅主体是毛毡，**背景、前景、甚至虚化的部分都必须是毛毡材质**。
3. **面部特征强锚定原则（核心更新）**：**严禁**使用“可爱的娃娃脸 (cute doll face)”等概括词来糊弄人像。必须精确提取原图的脸型（圆/长/方）、标志性表情（大笑/露齿/闭眼）、眉眼特征，并在正面提示词中显式写出。
4. **材质具体化原则**：用 `organic roving wool` 而非泛化的 `wool`。皮肤材质用 `matte felted skin` 以确保有毛毡感但不失五官立体度。
5. **平台锁脸原则（核心更新）**：必须在平台参数中强制要求使用锁脸工具（如 MJ 的 `--cref`，SD 的 `IP-Adapter FaceID/InstantID`），不能仅靠文字描述来还原长相。

---

## 1. 阶段一：客观结构提取（骨架层）

**目标**：剥离原图的风格、材质和情绪，仅提取构图骨架，**特别是面部特征**。

### 1.1 提取要素表
- **主体 (Subject)**：性别、年龄段、**脸型轮廓**。
- **面部特征 (Facial Features)**：**眼部（单/双、睁/闭/笑眼）**、**嘴部（张开大笑/抿嘴/露齿）**、眉毛形状、肤色。
- **发型与配饰 (Hair & Accessories)**：发质（爆炸头/直发/卷发）、发饰、首饰。
- **服装 (Clothing)**：主色调、花纹、特殊结构（如披肩、腰带）。
- **场景 (Scene)**：前景、背景物品。
- **光向与色调**。

### 1.2 骨架输出示例（以用户提供的非洲女性为例）
```json
{
  "subject": "年轻黑人女性，瘦长脸型",
  "facial_features": "张开嘴大笑，露出整齐的牙齿，眼睛笑成弯月状，肤色偏深",
  "hair_accessories": "爆炸头式浓密卷发，佩戴黄色和红色图案的民族风发带，白色美甲，银色项链",
  "clothing": "白色长袍，带有绿色和红色的刺绣，腰间系黄红相间的腰带",
  "scene": "背景是模糊的浅色建筑、黄色柱子、蓝绿色面包车",
  "light_direction": "柔和自然光，侧逆光",
  "color_tone": "暖调，白色、黑色、黄色、蓝绿色"
}
```

---

## 2. 阶段二：羊毛毡材质注入与特征保全（血肉层）

### 2.1 全局材质锚定词（必加）
- `entire scene is handcrafted felt`
- `felted background elements`

### 2.2 六大核心特征（必填）
| 维度 | 必含英文关键词 | 说明 |
|---|---|---|
| 纤维质感 | `visible wool fibers`, `organic roving wool` | 确保是羊毛毡而非塑料 |
| 边缘处理 | `soft fuzzy edges`, `uneven handmade edges` | 消除锐利感 |
| 光影质感 | `matte finish`, `soft diffused lighting` | 消除高光 |
| 手作痕迹 | `handcrafted seams`, `uneven stitching` | 增加手工温度 |
| 针戳纹理 | `visible needle-punch textures`, `needle marks` (针毡专属) | 区分湿毡 |
| **皮肤质感 (人像专属)** | **`matte felted skin, visible wool skin texture`** | **替代“光滑皮肤”，既保留人像特征，又符合毛毡材质** |

### 2.3 面部特征强锚定（人像/宠物必填）
**严禁**用 `cute face`。必须把第一阶段提取的面部特征翻译为英文：
- **表情**：`laughing widely`, `showing teeth`, `crescent-shaped smiling eyes`, `closed eyes`.
- **脸型**：`long oval face`, `round face`, `defined jawline`.
- **五官**：`thick eyebrows`, `defined nose`, `detailed felted lips`.
- *组合示例*：`A handcrafted felted doll of a young Black woman with a long oval face, laughing widely showing white felted teeth, her eyes shaped like crescent moons, matte felted skin with visible wool textures.`

---

## 3. 阶段三：提示词组装与输出

### 3.1 正面提示词组装公式 (严格按顺序)
```
[人物面部特征与表情描述] 
+ [服装与配饰描述] 
+ [场景描述] 
+ [全局材质锚定词] 
+ [维度词(3D微缩模型)] 
+ [工艺词(针毡)] 
+ [六大核心材质特征] 
+ [色彩与光影词] 
+ [氛围词] 
+ [镜头词]
```

### 3.2 负面提示词 (解决“不像”的问题)
**通用基础负面（必带）**：
```
smooth, glossy, shiny, plastic, metallic, sharp edges, realistic lighting, photorealistic, 3D render, digital painting, airbrushed, plush toy, crochet, knitted cloth, woven fabric, real background, photo background, depth of field blur, neat stitching, needle, sewing needle
```

**人像专防面部走样（必带）**：
```
generic doll face, exaggerated expression, childlike proportions, chubby face, closed mouth (如果原图是张嘴笑), missing teeth, creepy doll, uncanny valley, smooth plastic face, realistic human skin texture, different person, wrong face
```
*(注：如果原图是张嘴笑，必须在负面词中加入 `closed mouth`，强制 AI 画开嘴)*

### 3.3 平台适配参数（锁脸实操）

#### Midjourney
- **必须使用 `--cref` (角色参考)**：`--cref [原图URL] --cw 100` (cw 100 表示参考脸部和头发特征)。
- **必须添加 `--style raw`**：关闭默认美化。
- **风格化值**：`--s 50` 到 `--s 100`。
- **长宽比**：`--ar 3:4`（人像常用）。

#### Stable Diffusion / ComfyUI (核心)
- **IP-Adapter FaceID / InstantID**：权重设为 `0.7 - 0.9`。**这是保脸的绝对核心**，只有文字提示词很难完全还原长相。
- **ControlNet (SoftEdge)**：权重 `0.5` 锁轮廓。
- **重绘幅度 (Denoising)**：**`0.50 - 0.60`**。人像重绘幅度绝对不能太高（不要超过0.65），否则五官结构会崩塌。
- **LoRA (Woolfelt)**：权重 `0.6 - 0.8`。

---

## 4. 针对用户提供图片的完整修复方案示例

**区块 1：输入确认**
> 原图为一张黑人女性大笑的照片。问题：AI 生成图丢失了原图的“咧嘴大笑、露齿、弯眼”和瘦长脸型。现按 **3D 针毡 + 面部强锚定** 重新生成。

**区块 2：骨架提取** (略，见前文)

**区块 3：面部与细节翻译说明**
- 真实皮肤 → `matte felted skin, visible wool skin texture` (保留毛毡感)
- 张嘴大笑 → `laughing widely, mouth open, showing white felted teeth` (强制开嘴)
- 弯弯的笑眼 → `crescent-shaped smiling eyes` (特征提取)
- 爆炸头 → `voluminous tight curly black wool hair` (毛毡化)
- 白色衣服的刺绣 → `white felted dress with green and red embroidered patterns` (细节保留)

**区块 4：正面提示词 (直接复制使用)**
```text
A 3D needle felted sculpture of a young Black woman with a long oval face, laughing widely with her mouth open, showing neat white felted teeth, her eyes shaped like crescent moons. She has voluminous tight curly black wool hair with a yellow and red patterned felted headband, long white fingernails, wearing a white felted dress with green and red embroidered patterns, a yellow and red sash around her waist. The background is a blurred street scene with a light-colored building, a yellow pole, and a teal felted van. Entire scene is handcrafted felt. 3D miniature diorama. Needle felted, visible needle-punch textures, needle marks, organic roving wool. Visible wool fibers, soft fuzzy edges, uneven handmade edges. Matte felted skin, matte finish, soft diffused lighting, uneven stitching. Pastel and muted warm tones. Shot on 90mm macro lens at f/2.8, shallow depth of field. --style raw --s 100 --ar 3:4 --cref [原图链接] --cw 100
```

**区块 5：负面提示词 (直接复制使用)**
```text
smooth, glossy, shiny, plastic, metallic, sharp edges, sharp focus, high contrast, realistic lighting, photorealistic, 3D render, digital painting, airbrushed, plush toy, crochet, knitted cloth, woven fabric, real background, photo background, depth of field blur, neat stitching, needle, sewing needle, generic doll face, exaggerated expression, childlike proportions, chubby face, closed mouth, missing teeth, creepy doll, uncanny valley, smooth plastic face, realistic human skin texture, wrong face, different person, lowres, blurry, watermark
```
*(注意：负面词里加了 `closed mouth` 和 `chubby face`，专门防图1那种闭眼微笑的圆脸娃娃)*

**区块 6：参数备注**
- Midjourney：**必须使用 `--cref` 垫图锁脸**，否则单靠文字很难还原长相。
- SD 用户：**必须开启 IP-Adapter FaceID**，重绘幅度降到 0.55 左右，配合毛毡 LoRA。

**区块 7：验收清单**
- [x] `matte felted skin` (替换了真实皮肤)
- [x] `laughing widely, mouth open` (锁定了表情)
- [x] `long oval face` (锁定了脸型)
- [x] 负面词包含了 `closed mouth` 和 `chubby face`
- [x] 使用了平台锁脸参数 (`--cref` / IP-Adapter)