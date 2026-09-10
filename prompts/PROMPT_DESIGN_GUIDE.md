# Krea 2 Turbo INT4 游戏 UI 提示词设计完全指南

## 📋 目录
1. [提示词设计原理](#原理)
2. [核心公式](#核心公式)
3. [元素库](#元素库)
4. [完整提示词示例](#完整示例)
5. [常见问题](#常见问题)

---

## 原理

### Krea 2 的提示词特点

```
✅ 支持复杂描述 (最好 50-150 tokens)
✅ 对质感描述敏感
✅ 强调细节效果
✅ 支持多重修饰符
✅ 颜色描述精准
❌ 避免：photorealistic, realistic people
❌ 避免：过度复杂的句子
```

### 提示词结构权重

```
总权重: 100%

[风格定位]        15% ← 游戏 UI 或概念艺术
[主体元素]        25% ← 按钮/框架/图标
[质感描述]        20% ← 金属/玻璃/布料
[效果修饰]        20% ← 发光/阴影/深度
[色彩指示]        10% ← 配色方案
[质量修饰符]      10% ← 分辨率/细节度
```

---

## 核心公式

### 标准 UI 生成公式

```
[游戏类型] [UI类型], [主要特征], 
[质感1], [质感2], [效果1], [效果2], 
[色彩描述], [质量修饰符]
```

### 示例结构

```
"A fantasy RPG game menu button, ornate gold frame, 
embossed 3D surface, metallic shine, glowing edges, 
rich red and gold colors, professional game asset, 
high quality, detailed, sharp"
```

---

## 元素库

### 🎮 游戏风格定位 (第一段必选一个)

#### 类型标签
```
# RPG 奇幻
- A fantasy RPG game UI
- A dark fantasy game interface
- A medieval RPG style
- A high-fantasy game UI element

# 科幻/未来
- A sci-fi futuristic game UI
- A cyberpunk game interface
- A neon-style game panel
- A techno-military game UI

# 卡牌/棋牌
- A trading card game UI frame
- A collectible card interface
- A board game element
- A card game card sleeve

# 休闲/2D
- A cute casual game UI
- A mobile game interface
- A puzzle game element
- A 2D platformer UI

# 动作/FPS
- A shooter game HUD element
- A tactical action game UI
- An esports-style interface
- A real-time strategy panel
```

### 🎨 具体 UI 元素 (主要描述)

```
# 按钮类
- game button, interactive element
- menu button with depth
- clickable UI button
- toggle switch
- action button

# 框架/容器
- game panel background
- UI window frame
- information display box
- character stat frame
- inventory slot

# 进度/数值
- health bar, mana bar
- experience progress bar
- cooldown timer
- loading bar
- status indicator

# 装饰元素
- avatar frame, portrait border
- achievement badge
- skill icon frame
- item frame
- character nameplate

# 特殊元素
- minimap UI element
- crosshair/reticle
- skill hotbar
- quest marker
- dialogue box
```

### 🌟 质感描述库 (核心差异化)

#### 金属质感组
```
# 基础金属
- metallic surface
- brushed metal finish
- polished chrome
- steel texture
- iron material

# 贵金属
- gold texture, golden sheen
- silver surface, silvery glow
- bronze material
- copper shine
- platinum finish

# 破损/古旧
- aged metal, worn texture
- rusty surface
- tarnished finish
- patina effect
- weathered metal
```

#### 玻璃/透明质感
```
- glass material, glossy surface
- transparent glass panel
- frosted glass effect
- tinted glass
- crystalline surface
- translucent layer
- gloss reflection
- mirror-like shine
```

#### 其他质感
```
# 布料/纺织
- fabric texture, woven material
- silk surface, silky smooth
- leather surface, leather strap
- velvet material
- canvas texture

# 陶瓷/石头
- ceramic material
- porcelain surface
- stone texture
- marble effect
- granite finish

# 其他
- plastic material
- rubber surface
- wood grain
- crystal formation
- gemstone surface
```

### ✨ 视觉效果库 (发光/阴影/动态)

```
# 发光/光晕
- glowing edges, luminescent border
- neon glow, neon outline
- light emission, radiant
- soft glow, ambient glow
- bright highlight
- glowing particles

# 阴影/深度
- soft shadow, depth shadow
- drop shadow effect
- ambient occlusion
- shadow depth, dimensional
- subtle shading
- depth gradient

# 其他效果
- beveled edge, 3D beveled
- embossed surface, embossed detail
- engraved pattern
- raised relief
- inlay detail
- texture overlay
- motion blur
- particle effects
- magical sparkle
- holographic effect
```

### 🎨 色彩描述库 (精准配色)

```
# 基础颜色组
- gold and crimson
- blue and silver
- green and black
- purple and gold
- red and gold gradient

# 具体色值
- deep blue, bright cyan
- warm gold, cold silver
- rich red, dark maroon
- emerald green, forest green
- royal purple, amethyst

# 色彩效果
- vibrant color, saturated
- muted tone, desaturated
- pastel color, soft palette
- warm color scheme
- cool color tone
- color gradient, color shift
```

### 🎯 质量修饰符 (最后必加)

```
# 分辨率
- 4K quality (推荐)
- 2K resolution
- high resolution
- sharp quality
- crisp detail

# 细节度
- detailed, intricate detail
- fine detail, fine texture
- elaborate design
- precise, sharp lines
- clean design
- minimalist design

# 专业度
- professional game asset
- AAA game quality
- game ready asset
- production quality
- concept art quality
- illustrator style
```

---

## 完整示例

### 🎯 示例 1：奇幻 RPG 主菜单按钮

**分析：**
- 风格：奇幻RPG
- 元素：按钮（带 3D 效果）
- 质感：金属 + 宝石
- 效果：发光 + 阴影
- 色彩：金红配色
- 质量：4K 游戏资源

**Positive 提示词：**
```
A fantasy RPG game menu button, ornate gold-trimmed frame, 
embossed 3D beveled surface, rich crimson center with golden swirls, 
metallic polish finish with soft glow, glowing edge highlight, 
subtle shadow depth, intricate decorative pattern, elegant detail, 
professional game asset quality, 4K, sharp and crisp, high quality
```

**Negative 提示词：**
```
photorealistic, realistic people, blurry, distorted, ugly, 
low quality, text, watermark, pixelated, 3D model, 
organic shapes, soft edges, inconsistent design
```

**参数设置：**
```
Steps: 20
CFG: 1.0
Sampler: Euler
Scheduler: Simple
Seed: 固定值（例：12345）
Resolution: 1024x1024
```

---

### 🎯 示例 2：赛博朋克未来科幻面板

**分析：**
- 风格：科幻 / 赛博朋克
- 元素：面板 / 窗口框架
- 质感：玻璃 + 金属
- 效果：霓虹光晕 + 几何线条
- 色彩：青色 / 品红色
- 质量：高清游戏资源

**Positive 提示词：**
```
A cyberpunk sci-fi game UI panel, sleek dark background, 
neon cyan and magenta glowing border, sharp geometric edges, 
glass-morphism effect with transparency, metallic frame accent, 
glowing grid pattern, holographic projection style, 
minimalist futuristic design, clean sharp lines, 
professional esports aesthetic, game ready asset, 2K quality, crisp detail
```

**Negative 提示词：**
```
photorealistic, blurry, organic shapes, watermark, text, 
soft edges, low quality, realistic, 3D rendering
```

**参数设置：**
```
Steps: 20
CFG: 1.0
Sampler: Euler
Scheduler: Simple
Resolution: 1024x1024
```

---

### 🎯 示例 3：卡牌游戏框架

**Positive 提示词：**
```
A collectible trading card game UI frame, hexagonal shaped border, 
ornate decorative corner accents, gold metallic frame, 
semi-transparent glass center panel, 
glowing neon accent lines in purple and blue, 
intricate embossed pattern around edge, holographic shimmer effect, 
elegant luxury aesthetic, professional card game asset, 
4K quality, sharp lines, high detail, game ready
```

**Negative 提示词：**
```
photorealistic, blurry, text, watermark, organic, 
soft edges, low quality, pixelated, 3D model
```

---

### 🎯 示例 4：生命值进度条

**Positive 提示词：**
```
RPG game health bar UI element, horizontal bar design, 
gradient red to dark red color, metallic protective frame border, 
glowing edge highlight, subtle shadow depth below, 
professional game asset quality, clean design, 
high quality, sharp, detailed, no background, transparent
```

**Negative 提示词：**
```
photorealistic, 3D, text, watermark, blurry, 
complicated design, organic shapes
```

---

### 🎯 示例 5：头像框架装饰

**Positive 提示词：**
```
Cute character avatar frame, ornate pastel pink and gold border, 
soft glowing edge frame, decorative ribbon and star details, 
kawaii anime aesthetic, warm peachy lighting, 
illustrated style portrait frame, professional game UI asset, 
high quality, sharp, detailed, gentle color palette, 
illustrated not photorealistic
```

**Negative 提示词：**
```
photorealistic, dark, aggressive, pixelated, 
low resolution, 3D model, realistic people
```

---

### 🎯 示例 6：冷却计时器

**Positive 提示词：**
```
Cooldown timer UI element, circular design, 
glowing bright green progress ring indicator, 
futuristic numeric display, sci-fi military aesthetic, 
sharp geometric frame, minimal clean design, 
tactical interface style, game ready asset, 
2K quality, crisp sharp lines, detailed, 
transparent background
```

**Negative 提示词：**
```
photorealistic, text errors, blurry, complicated, 
watermark, organic shapes, soft edges
```

---

### 🎯 示例 7：装备栏槽位

**Positive 提示词：**
```
RPG game inventory slot frame, square grid element, 
deep dark background, glowing blue accent border, 
embossed frame with highlight reflection, 
metallic shine surface, professional game asset, 
detailed intricate pattern, sharp quality, 
4K resolution, clean design, high detail
```

**Negative 提示词：**
```
photorealistic, blurry, text, watermark, 
soft edges, low quality, realistic
```

---

### 🎯 示例 8：任务标记 / 指针

**Positive 提示词：**
```
Game quest marker indicator icon, star burst design, 
glowing golden center with radiating light rays, 
magical sparkle particles, bright yellow and white glow, 
fantasy aesthetic, clean simple shape, 
professional game asset quality, high quality, 
sharp detailed, transparent background
```

**Negative 提示词：**
```
photorealistic, blurry, text, 3D model, 
organic shapes, low quality, pixelated
```

---

## 提示词设计流程

### 第 1 步：确定游戏类型和风格

```
问自己：
❓ 这是什么游戏？(RPG/FPS/卡牌/休闲)
❓ 总体风格？(奇幻/科幻/卡通/写实)
❓ 目标受众？(硬核/休闲/儿童)
❓ UI 的主要特征？(华丽/简洁/粗糙)

结果：选择合适的风格标签
例：A fantasy RPG game UI / A cyberpunk sci-fi interface
```

### 第 2 步：定义主要元素

```
问自己：
❓ UI 的具体类型？(按钮/框/图标)
❓ 主要形状？(圆形/方形/不规则)
❓ 大小关系？(小巧/庞大/中等)
❓ 交互方式？(点击/滑动/悬停)

结果：选择精确的元素描述
例：ornate menu button / sleek control panel
```

### 第 3 步：添加质感层次

```
��选 1-2 个主质感：
- 金属类（金/银/铜/钢）
- 玻璃类（透明/磨砂/彩色）
- 布料类（丝绸/皮革/绒布）

例：metallic gold frame with glass center panel
```

### 第 4 步：加入视觉效果

```
选择 2-3 个效果：
- 发光类：glowing edges, neon glow
- 阴影类：soft shadow, depth effect
- 立体类：beveled, embossed, 3D effect

例：glowing edge highlight with soft shadow depth
```

### 第 5 步：确定色彩方案

```
选择一个主色组合：
- 暖色：gold + crimson / orange + yellow
- 冷色：cyan + purple / blue + silver
- 中性：black + gray + white + gold

例：rich red and gold colors
或：neon cyan and magenta with glowing effect
```

### 第 6 步：添加质量修饰符

```
必须在结尾添加：
[分辨率] + [细节度] + [专业度]

标准组合：
✅ 4K quality, sharp, detailed, professional game asset
✅ 2K resolution, crisp, intricate detail, high quality
✅ high quality, sharp detail, game ready asset
```

### 第 7 步：编写 Negative 提示词

```
固定模板：
photorealistic, realistic people, blurry, distorted, ugly, 
low quality, text, watermark, [特定要避免的东西], 
pixelated, 3D model, organic shapes, soft edges
```

---

## 📊 提示词���板速查表

### 快速生成模板

```
# 通用模板
"A [游戏类型] game [UI类型], [主要特征], 
[质感1], [质感2], [效果], [色彩], 
[质量修饰符]"

# 奢华风
"A [game type] UI, ornate [material], 
embossed detail, glowing [color] accent, 
metallic polish, professional asset, 4K quality"

# 极简风
"A [game type] UI, minimalist design, 
clean sharp lines, [primary color] accent, 
flat material, game ready, 2K quality, crisp"

# 科幻风
"A sci-fi game UI, geometric frame, 
neon [color] glow, glass effect, 
sharp edges, futuristic, game asset, 4K"

# 奇幻风
"A fantasy game UI, ornate border, 
magical sparkle, [material] texture, 
glowing edge, detailed, game quality, 4K"
```

---

## 常见问题

### Q1：生成的 UI 文字/文本太清晰，混淆了

**原因：** Krea 2 对文本的识别能力强

**解决方案：**
```
❌ 删除 "sharp text", "clear letters"
✅ 在 negative 中加 "text, letters, words"
✅ 在 positive 中改为 "no text elements"
```

### Q2：颜色不对

**原因：** 色彩描述不精确

**改进方案：**
```
❌ 不好："red button"
✅ 好的："rich crimson red with gold swirl"
✅ 更好："deep ruby red transitioning to burnished gold"

颜色描述层次：
基础色 → 修饰词 → 效果描述
red → rich crimson red → with metallic shine
```

### Q3：效果太夸张

**原因：** 效果修饰符过多

**简化方案：**
```
❌ 太多："glowing, luminescent, radiant, sparkling, magical..."
✅ 精准：选择 1-2 个最重要的
例："glowing edges with soft shadow depth" (只选 2 个)
```

### Q4：生成速度慢

**不是提示词问题，是模型问题**
- INT4 量化的固有成本（35-50秒）
- 使用 --lowvram 会更慢
- 无法优化，这是正常的

### Q5：质量不如 FLUX/SD3

**原因：** Krea 2 INT4 是 INT4 量化版本

**改进方案：**
```
✅ 优化提示词（按照上面的指南）
✅ 增加 steps (20→25)
✅ 调整 CFG (1.0→1.2)
✅ 升级到 FP8 版本（如果 VRAM 允许）

但要接受：INT4 量化 = 质量略降
```

### Q6：如何让 UI 看起来更游戏化

**核心要点：**
```
❌ 避免的词汇：
- photorealistic
- realistic
- true to life
- cinematic
- camera quality

✅ 使用的词汇：
- game asset quality
- professional game UI
- game ready
- illustrated style
- concept art quality
- AAA game quality
```

### Q7：如何生成特定的几何形状

**形状指定方法：**
```
圆形：circular design, round frame, rounded corners
方形：square shape, rectangular frame, angular
六边形：hexagonal shape, hexagon frame
星形：star shape, star burst, star pattern
有机形：organic shapes (但一般要避免)

例："A circular cooldown timer with hexagonal frame accent"
```

### Q8：提示词应该多长

**最优长度：**
```
理想范围：60-120 tokens (大约 50-100 个单词)

太短：<40 tokens
  问题：细节不足，效果模糊

刚好：60-120 tokens
  ✅ 推荐这个范围

太长：>150 tokens
  问题：Krea 2 可能无法完全处理，细节混乱
```

### Q9：Seed 值的影响

**Seed 使用指南：**
```
固定 Seed (例如：12345)：
✅ 每次生成相同的图像
✅ 用于微调提示词
✅ 用于版本控制

随机 Seed (例如：-1)：
✅ 每次生成不同的图像
✅ 用于多样性探索
✅ 用于批量生成变体

最佳实践：
1. 用固定 seed 微调提示词
2. 提示词满意后，用随机 seed 批量生成
```

### Q10：如何让多个 UI 风格一致

**一致性关键：**
```
核心要素要相同：
- 相同的游戏类型标签
- 相同的主要质感
- 相同的色彩方案
- 相同的风格标签

例：所有按钮都用
"fantasy RPG game UI, ornate gold frame, 
metallic polish, glowing edge, rich red and gold"
```

---

## 🎯 快速开始提示词

### 复制即用的 5 个提示词

#### 1️⃣ 标准 RPG 按钮（推荐新手）
```
Positive:
A fantasy RPG game menu button, ornate gold frame, 
3D beveled surface, metallic shine, soft glow edges, 
crimson red center, professional game asset, 
4K quality, sharp, detailed

Negative:
photorealistic, blurry, text, low quality, 
organic shapes, soft edges
```

#### 2️⃣ 科幻面板（高级）
```
Positive:
A cyberpunk sci-fi game UI panel, neon cyan border, 
glass-morphism effect, metallic frame, 
sharp geometric edges, glowing accent lines, 
game ready asset, 2K quality, crisp detail

Negative:
photorealistic, blurry, organic, watermark, low quality
```

#### 3️⃣ 进度条（简单）
```
Positive:
RPG health bar, red gradient, metal frame, 
shadow depth, professional game asset, high quality

Negative:
photorealistic, 3D, text, blurry, complicated
```

#### 4️⃣ 头像框（可爱风）
```
Positive:
Character avatar frame, pastel pink and gold border, 
soft glow, decorative ribbon, kawaii style, 
game UI asset, high quality, detailed

Negative:
photorealistic, dark, pixelated, low quality
```

#### 5️⃣ 计时器（极简）
```
Positive:
Cooldown timer, circular green ring, 
futuristic design, clean sharp, game asset, 
2K quality, crisp

Negative:
photorealistic, text, blurry, complicated
```

---

## 📈 提示词优化流程

```
初稿 → 生成测试 → 评估 → 微调 → 最终版

微调检查清单：
□ 颜色是否符合预期？
□ 效果是否过度或不足？
□ 质感是否清晰可见？
□ 形状是否准确？
□ 整体风格是否统一？
□ 质量修饰符是否有效？
```

---

## 💡 高级技巧

### 技巧 1：使用权重调整（如果支持）

```
如果 Krea 2 支持 (attention 权重)：
(important_feature:1.3) 强调这个特征
(minor_detail:0.7) 降低这个细节
```

### 技巧 2：对比学习

```
生成同一个 UI 的两个版本：
版本 A："simple, clean, minimal"
版本 B："ornate, detailed, elaborate"

比较结果，选择更接近目标的方向
```

### 技巧 3：分层迭代

```
第一轮：基础 UI 生成
第二轮：调整色彩
第三轮：增强效果
第四轮：最终优化

每轮只改一个方面，便于追踪
```

---

**现在开始使用这些提示词创建你的游戏 UI 资源库吧！** 🎮✨
