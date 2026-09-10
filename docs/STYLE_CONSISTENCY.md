# 单独生成 UI 元素的风格统一性完全指南

## 🎯 核心问题

**单独生成的风格不统一主要原因：**

```
❌ 问题 1：提示词不一致
   - 每个元素用了不同的描述方式
   - 风格标签不同
   - 质感描述差异大

❌ 问题 2：模型随机性
   - 即使提示词相同，也会有细微变化
   - Seed 不固定导致结果完全不同
   - 量化模型本身有内在噪声

❌ 问题 3：参数波动
   - 采样步数不同
   - CFG 值变化
   - 采样器不同

❌ 问题 4：审美差异
   - 颜色饱和度波动
   - 发光强度不一致
   - 阴影深度变化
```

---

## ✅ 解决方案：风格统一化系统

### 方案概览

```
3 个关键策略：
1️⃣ 统一的"风格模板" (基础骨架)
2️⃣ 固定的全局参数 (Seed + 采样参数)
3️⃣ 递进式 Seed 管理 (保证多样性又一致)
```

---

## 1️⃣ 风格模板系统（最重要）

### 核心概念：基础提示词 + 变量替换

```
不要这样写提示词：
❌ 每个元素单独写完整提示词
   按钮：A fantasy RPG game menu button...
   框框：A fantasy RPG inventory frame...
   条条：RPG game health bar...
   
✅ 应该这样写：
   定义统一的"风格基础句"
   其他部分用变量替换
```

### 风格模板结构

```python
# 定义一个游戏项目的统一风格模板

STYLE_TEMPLATE = {
    "project_name": "Fantasy RPG",
    "base_style": "A fantasy RPG game",           # 统一风格标签
    "base_material": "ornate gold frame",          # 统一质感
    "base_effect": "metallic polish with soft glow", # 统一效果
    "base_color": "rich red and gold",             # 统一配色
    "base_quality": "professional game asset, 4K, sharp, detailed"  # 统一质量
}

# 生成提示词时用模板
def generate_prompt(element_type, element_name):
    if element_type == "button":
        return f"{STYLE_TEMPLATE['base_style']} {element_name}, " \
               f"{STYLE_TEMPLATE['base_material']}, " \
               f"3D beveled surface, " \
               f"{STYLE_TEMPLATE['base_effect']}, " \
               f"{STYLE_TEMPLATE['base_color']}, " \
               f"{STYLE_TEMPLATE['base_quality']}"
    
    elif element_type == "frame":
        return f"{STYLE_TEMPLATE['base_style']} {element_name}, " \
               f"{STYLE_TEMPLATE['base_material']} border, " \
               f"{STYLE_TEMPLATE['base_effect']}, " \
               f"{STYLE_TEMPLATE['base_color']}, " \
               f"{STYLE_TEMPLATE['base_quality']}"
    
    elif element_type == "bar":
        return f"{STYLE_TEMPLATE['base_style']} {element_name}, " \
               f"gradient color bar, metal frame, " \
               f"{STYLE_TEMPLATE['base_effect']}, " \
               f"{STYLE_TEMPLATE['base_quality']}"
```

### 完整模板示例库

#### 模板 A：奇幻 RPG 风格

```python
FANTASY_RPG = {
    "name": "Fantasy RPG",
    "base_style": "A fantasy RPG game",
    "base_material": "ornate gold-trimmed frame with embossed surface",
    "base_texture": "metallic polish finish",
    "base_effect": "soft glowing edges, subtle shadow depth",
    "base_color": "rich red and gold colors",
    "base_quality": "professional game asset quality, 4K, sharp, detailed"
}

# 应用示例
button_prompt = f"{FANTASY_RPG['base_style']} menu button, " \
                f"{FANTASY_RPG['base_material']}, " \
                f"3D beveled, " \
                f"{FANTASY_RPG['base_texture']}, " \
                f"{FANTASY_RPG['base_effect']}, " \
                f"{FANTASY_RPG['base_color']}, " \
                f"{FANTASY_RPG['base_quality']}"

frame_prompt = f"{FANTASY_RPG['base_style']} inventory frame, " \
               f"{FANTASY_RPG['base_material']} border, " \
               f"{FANTASY_RPG['base_texture']}, " \
               f"{FANTASY_RPG['base_effect']}, " \
               f"{FANTASY_RPG['base_color']}, " \
               f"{FANTASY_RPG['base_quality']}"
```

#### 模板 B：科幻赛博朋克风格

```python
CYBERPUNK_SCI_FI = {
    "name": "Cyberpunk Sci-Fi",
    "base_style": "A cyberpunk sci-fi game",
    "base_material": "sleek dark background with metallic frame",
    "base_texture": "glass-morphism effect, polished chrome",
    "base_effect": "neon cyan glowing border, sharp geometric glow",
    "base_color": "neon cyan and magenta with glowing effects",
    "base_quality": "professional game asset, 2K quality, crisp, sharp"
}
```

#### 模板 C：卡通可爱风格

```python
CUTE_CASUAL = {
    "name": "Cute Casual",
    "base_style": "A cute casual game",
    "base_material": "soft rounded border with pastel colors",
    "base_texture": "smooth glossy surface",
    "base_effect": "soft glow, gentle highlight",
    "base_color": "pastel pink and cream colors",
    "base_quality": "professional game asset, high quality, gentle aesthetic"
}
```

---

## 2️⃣ 固定全局参数（保证可重现性）

### 参数固定清单

```python
class UnifiedUIGenerator:
    # ===== 固定的全局参数 =====
    FIXED_STEPS = 20              # 所有元素用相同步数
    FIXED_CFG = 1.0               # Krea 2 推荐值
    FIXED_SAMPLER = "euler"       # 所有元素用同一采样器
    FIXED_SCHEDULER = "simple"    # Krea 2 推荐
    FIXED_MODEL = "krea2_turbo_int4_tensorwise_mixed.safetensors"
    FIXED_CLIP = "qwen3vl_4b_int4_convrot.safetensors"
    FIXED_VAE = "qwen_image_vae.safetensors"
    
    # ===== 全局 Seed 管理 =====
    BASE_SEED = 42               # 项目基础种子
    SEED_INCREMENT = 1           # 每个元素递增 1
    
    def __init__(self):
        self.current_seed = self.BASE_SEED
        self.negative_prompt = (
            "photorealistic, realistic people, blurry, distorted, "
            "ugly, low quality, text, watermark, pixelated, "
            "3D model, organic shapes, soft edges, inconsistent design"
        )
    
    def get_next_seed(self):
        """获取下一个 Seed，保证有变化又有联系"""
        seed = self.current_seed
        self.current_seed += 1
        return seed
```

### 为什么这样做？

```
✅ 固定 steps, cfg, sampler
   → 确保每个元素的处理流程完全相同
   → 消除参数导致的风格差异

✅ 统一 negative_prompt
   → 所有元素都避免相同的问题
   → 一致的"不要什么"

✅ 递进式 Seed (42, 43, 44, 45...)
   → 每个元素都有微妙的变化（显得自然）
   → 但又保持关联性（基于同一个基础种子）
   → 不是完全随机（0, 999, 12345... 完全无关)

结果：
- 元素 1: Seed 42 ← 略微细节 A
- 元素 2: Seed 43 ← 略微细节 B（但风格基调相同）
- 元素 3: Seed 44 ← 略微细节 C（但风格基调相同）
```

---

## 3️⃣ 递进式 Seed 管理策略

### 种子管理的三个层级

```
第 1 层：项目级种子
├─ 每个项目有唯一的 BASE_SEED
├─ 例：Fantasy RPG = 42, Cyberpunk = 100, Cute = 200
└─ 作用：项目间风格完全不同

第 2 层：套装级种子
├─ 同一个"套装"内的元素共享基础
├─ 例：主菜单按钮套装 (5个按钮) = 42-46
├─ 例：进度条套装 (3个条) = 47-49
└─ 作用：同一套装的元素风格高度统一

第 3 层：元素级种子
├─ 每个元素有微妙的独特性
├─ 通过递增实现
└─ 作用：避免完全重复
```

### 代码实现

```python
class SeededUIGenerator:
    def __init__(self, project_name: str, base_seed: int):
        self.project_name = project_name
        self.base_seed = base_seed
        self.current_seed = base_seed
        self.element_count = 0
    
    def get_seed(self, element_id: str) -> int:
        """获取当前元素的种子"""
        seed = self.current_seed
        self.current_seed += 1
        self.element_count += 1
        print(f"Element #{self.element_count} ({element_id}): Seed = {seed}")
        return seed
    
    def reset_suite(self):
        """重置到下一个套装的起始种子"""
        # 可选：在不��的元素组之间重置
        pass

# 使用示例
generator = SeededUIGenerator("Fantasy RPG", base_seed=42)

# 生成按钮套装
button_seed_1 = generator.get_seed("button_main_menu")      # 42
button_seed_2 = generator.get_seed("button_settings")       # 43
button_seed_3 = generator.get_seed("button_inventory")      # 44

# 生成框架套装
frame_seed_1 = generator.get_seed("frame_inventory")        # 45
frame_seed_2 = generator.get_seed("frame_character")        # 46
frame_seed_3 = generator.get_seed("frame_quest_log")        # 47

# 生成条形套装
bar_seed_1 = generator.get_seed("bar_health")              # 48
bar_seed_2 = generator.get_seed("bar_mana")                # 49
bar_seed_3 = generator.get_seed("bar_stamina")             # 50
```

---

## 🎨 完整的统一风格生成系统

### 综合实现

```python
"""
统一风格 Krea 2 UI 生成系统
确保所有元素风格一致
"""

import json
from dataclasses import dataclass
from typing import Dict, Optional

@dataclass
class UIStyle:
    """UI 风格定义"""
    name: str
    base_style: str
    base_material: str
    base_texture: str
    base_effect: str
    base_color: str
    base_quality: str

# ===== 1. 定义风格库 =====
STYLE_LIBRARY = {
    "fantasy_rpg": UIStyle(
        name="Fantasy RPG",
        base_style="A fantasy RPG game",
        base_material="ornate gold-trimmed frame with embossed surface",
        base_texture="metallic polish finish",
        base_effect="soft glowing edges, subtle shadow depth",
        base_color="rich red and gold colors",
        base_quality="professional game asset quality, 4K, sharp, detailed"
    ),
    "cyberpunk": UIStyle(
        name="Cyberpunk Sci-Fi",
        base_style="A cyberpunk sci-fi game",
        base_material="sleek dark background with metallic frame",
        base_texture="glass-morphism effect, polished chrome",
        base_effect="neon cyan glowing border, sharp geometric glow",
        base_color="neon cyan and magenta with glowing effects",
        base_quality="professional game asset, 2K quality, crisp, sharp"
    ),
    "cute_casual": UIStyle(
        name="Cute Casual",
        base_style="A cute casual game",
        base_material="soft rounded border with pastel colors",
        base_texture="smooth glossy surface",
        base_effect="soft glow, gentle highlight",
        base_color="pastel pink and cream colors",
        base_quality="professional game asset, high quality, gentle aesthetic"
    )
}

# ===== 2. 统一生成器 =====
class UnifiedUIGenerator:
    def __init__(self, style_key: str, base_seed: int = 42):
        self.style = STYLE_LIBRARY[style_key]
        self.base_seed = base_seed
        self.current_seed = base_seed
        self.element_count = 0
        
        # 统一的参数
        self.steps = 20
        self.cfg = 1.0
        self.sampler = "euler"
        self.scheduler = "simple"
        self.negative_prompt = (
            "photorealistic, realistic people, blurry, distorted, "
            "ugly, low quality, text, watermark, pixelated, "
            "3D model, organic shapes, soft edges, inconsistent design"
        )
    
    def generate_prompt(self, element_type: str, element_name: str) -> str:
        """根据元素类型生成统一风格的提示词"""
        
        base = f"{self.style.base_style} {element_name}, "
        
        if element_type == "button":
            return (f"{base}"
                   f"{self.style.base_material}, "
                   f"3D beveled surface, "
                   f"{self.style.base_texture}, "
                   f"{self.style.base_effect}, "
                   f"{self.style.base_color}, "
                   f"{self.style.base_quality}")
        
        elif element_type == "frame":
            return (f"{base}"
                   f"{self.style.base_material} border, "
                   f"{self.style.base_texture}, "
                   f"{self.style.base_effect}, "
                   f"{self.style.base_color}, "
                   f"{self.style.base_quality}")
        
        elif element_type == "bar":
            return (f"{base}"
                   f"gradient bar with metal frame, "
                   f"{self.style.base_texture}, "
                   f"{self.style.base_effect}, "
                   f"{self.style.base_color}, "
                   f"{self.style.base_quality}")
        
        elif element_type == "icon":
            return (f"{base}"
                   f"{self.style.base_effect}, "
                   f"{self.style.base_color}, "
                   f"{self.style.base_quality}")
        
        else:
            raise ValueError(f"Unknown element type: {element_type}")
    
    def get_seed(self) -> int:
        """获取下一个种子（递增）"""
        seed = self.current_seed
        self.current_seed += 1
        self.element_count += 1
        return seed
    
    def create_workflow(
        self,
        element_type: str,
        element_name: str,
        width: int = 1024,
        height: int = 1024
    ) -> Dict:
        """创建完整的 ComfyUI 工作流"""
        
        prompt = self.generate_prompt(element_type, element_name)
        seed = self.get_seed()
        
        workflow = {
            "1": {
                "class_type": "CheckpointLoaderSimple",
                "inputs": {
                    "ckpt_name": "krea2_turbo_int4_tensorwise_mixed.safetensors"
                }
            },
            "2": {
                "class_type": "CLIPLoader",
                "inputs": {
                    "clip_name": "qwen3vl_4b_int4_convrot.safetensors",
                    "type": "krea2"
                }
            },
            "3": {
                "class_type": "VAELoader",
                "inputs": {
                    "vae_name": "qwen_image_vae.safetensors"
                }
            },
            "4": {
                "class_type": "TextEncodeKrea2",
                "inputs": {
                    "clip": ["2", 0],
                    "prompt": prompt,
                    "strength": 1.0,
                    "start": 0.0,
                    "mode": "before prompt"
                }
            },
            "5": {
                "class_type": "TextEncodeKrea2",
                "inputs": {
                    "clip": ["2", 0],
                    "prompt": self.negative_prompt,
                    "strength": 1.0,
                    "start": 0.0,
                    "mode": "before prompt"
                }
            },
            "6": {
                "class_type": "ModelSamplingFlux",
                "inputs": {
                    "model": ["1", 0],
                    "shift": 1.15,
                    "scale_factor": 0.5,
                    "width": width,
                    "height": height
                }
            },
            "7": {
                "class_type": "EmptySD3LatentImage",
                "inputs": {
                    "width": width,
                    "height": height,
                    "batch_size": 1
                }
            },
            "8": {
                "class_type": "KSampler",
                "inputs": {
                    "seed": seed,
                    "steps": self.steps,
                    "cfg": self.cfg,
                    "sampler_name": self.sampler,
                    "scheduler": self.scheduler,
                    "denoise": 1.0,
                    "model": ["6", 0],
                    "positive": ["4", 0],
                    "negative": ["5", 0],
                    "latent_image": ["7", 0]
                }
            },
            "9": {
                "class_type": "VAEDecode",
                "inputs": {
                    "samples": ["8", 0],
                    "vae": ["3", 0]
                }
            },
            "10": {
                "class_type": "SaveImage",
                "inputs": {
                    "filename_prefix": f"{element_type}_{element_name}",
                    "images": ["9", 0]
                }
            }
        }
        
        return workflow

# ===== 3. 使用示例 =====
if __name__ == "__main__":
    # 创建风格统一的 UI 生成器
    generator = UnifiedUIGenerator(style_key="fantasy_rpg", base_seed=42)
    
    # 定义要生成的元素
    ui_elements = [
        ("button", "main_menu"),
        ("button", "settings"),
        ("button", "inventory"),
        ("frame", "character_stats"),
        ("frame", "quest_log"),
        ("bar", "health"),
        ("bar", "mana"),
        ("icon", "sword"),
        ("icon", "shield"),
    ]
    
    # 生成所有元素
    workflows = []
    for element_type, element_name in ui_elements:
        workflow = generator.create_workflow(element_type, element_name)
        workflows.append({
            "element_id": f"{element_type}_{element_name}",
            "prompt": generator.style.base_style,
            "seed": generator.current_seed - 1,  # 获取刚刚用过的种子
            "workflow": workflow
        })
        print(f"✓ Generated workflow for {element_type}_{element_name} "
              f"(Seed: {generator.current_seed - 1})")
    
    # 保存到 JSON
    with open("unified_workflows.json", "w", encoding="utf-8") as f:
        json.dump(workflows, f, indent=2, ensure_ascii=False)
    
    print(f"\n✅ 共生成 {len(workflows)} 个工作流")
    print(f"✅ 所有元素使用统一风格: {generator.style.name}")
    print(f"✅ 种子范围: {42} - {generator.current_seed - 1}")
```

---

## 🎯 风格统一检查清单

### 生成前检查

```
□ 选择了统一的风格模板？
□ 所有元素的 base_style 相同？
□ 所有元素的 base_material 相同？
□ 所有元素的 base_effect 相同？
□ 所有元素的 base_quality 相同？
□ negative_prompt 完全一致？
□ steps, cfg, sampler 都固定了？
□ 使用了递进式 seed (42, 43, 44...)?
```

### 生成后检查

```
□ 所有元素的色彩是否协调？
□ 所有元素的质感是否一致？
□ 所有元素的发光强度是否相近？
□ 所有元素的阴影深度是否协调？
□ 整体是否有"套装感"？
□ 可以混搭使用吗？
```

---

## 📊 种子管理表

### 推荐的种子分配

```
项目: Fantasy RPG
├─ 主菜单按钮: Seed 42-46 (5个)
├─ 二级菜单按钮: Seed 47-51 (5个)
├─ HUD 框架: Seed 52-57 (6个)
├─ 进度条: Seed 58-62 (5个)
├─ 头像框: Seed 63-65 (3个)
├─ 图标装饰: Seed 66-85 (20个)
└─ 其他元素: Seed 86-100 (15个)

项目: Cyberpunk
├─ 核心按钮: Seed 200-204
├─ 面板框架: Seed 205-210
├─ HUD 元素: Seed 211-230
└─ 装饰元素: Seed 231-250

项目: Cute Casual
├─ 圆形按钮: Seed 400-404
├─ 框架: Seed 405-410
├─ 小图标: Seed 411-440
└─ 装饰: Seed 441-460
```

---

## 💡 实用技巧

### 技巧 1：快速风格调整

```python
# 如果觉得某个方面需要调整，只需改一个地方
STYLE_LIBRARY["fantasy_rpg"].base_effect = \
    "bright glowing edges, deep shadow depth"  # 增强效果

# 然后重新生成所有元素，风格会自动更新
# 因为所有元素都引用这个模板
```

### 技巧 2：创建风格变体

```python
# 基于现有风格创建变体
fantasy_rpg_gold = STYLE_LIBRARY["fantasy_rpg"].copy()
fantasy_rpg_gold.base_color = "bright gold and emerald"

fantasy_rpg_silver = STYLE_LIBRARY["fantasy_rpg"].copy()
fantasy_rpg_silver.base_color = "silver and sapphire"

# 现在可以生成同一游戏的两个配色方案
```

### 技巧 3：保存和导出风格

```python
# 将风格保存为 JSON，便于重复使用
def export_style(style: UIStyle, filename: str):
    style_dict = {
        "name": style.name,
        "base_style": style.base_style,
        "base_material": style.base_material,
        "base_texture": style.base_texture,
        "base_effect": style.base_effect,
        "base_color": style.base_color,
        "base_quality": style.base_quality
    }
    with open(filename, "w") as f:
        json.dump(style_dict, f, indent=2)

# 使用
export_style(STYLE_LIBRARY["fantasy_rpg"], "fantasy_rpg_style.json")
```

---

## 🎬 完整工作流示例

### 从零开始生成统一风格的 UI 库

```python
# 步骤 1：初始化生成器
generator = UnifiedUIGenerator(
    style_key="fantasy_rpg",
    base_seed=42
)

# 步骤 2：定义所有要生成的元素
ui_spec = [
    # 按钮组
    ("button", "main_menu_play", 1024, 512),
    ("button", "main_menu_settings", 1024, 512),
    ("button", "main_menu_exit", 1024, 512),
    
    # 框架组
    ("frame", "inventory", 768, 768),
    ("frame", "character_stats", 768, 1024),
    ("frame", "quest_log", 768, 1024),
    
    # 进度条组
    ("bar", "health_bar", 1024, 256),
    ("bar", "mana_bar", 1024, 256),
    ("bar", "stamina_bar", 1024, 256),
    
    # 图标组
    ("icon", "sword", 512, 512),
    ("icon", "shield", 512, 512),
    ("icon", "potion", 512, 512),
]

# 步骤 3：批量生成工作流
print(f"🎨 开始生成统一风格的 UI 资源...")
print(f"📋 风格: {generator.style.name}")
print(f"🔢 种子范围: 42 - {42 + len(ui_spec) - 1}\n")

for element_type, element_name, width, height in ui_spec:
    workflow = generator.create_workflow(
        element_type, element_name, width, height
    )
    seed = generator.current_seed - 1
    print(f"✓ {element_type:8} {element_name:25} (W:{width:4} H:{height:4}, Seed:{seed})")

print(f"\n✅ 共生成 {len(ui_spec)} 个工作流")
print(f"✅ 所有元素风格统一，可直接组合使用")
```

---

## 🏆 最佳实践总结

### DO ✅

```
✅ 1. 定义统一的风格模板（必须）
✅ 2. 固定所有参数（steps, cfg, sampler）
✅ 3. 使用递进式 seed（42, 43, 44...）
✅ 4. 同类型元素复用相同的基础提示词
✅ 5. 定期检查生成结果的一致性
✅ 6. 保存风格配置，便于后期调整
```

### DON'T ❌

```
❌ 1. 不要每个元素都写不同的提示词
❌ 2. 不要随意改变参数
❌ 3. 不要用完全随机的 seed
❌ 4. 不要混用不同的采样器
❌ 5. 不要改变 negative_prompt
❌ 6. 不要边生成边修改风格
```

---

**现在你有了完整的系统来确保单独生成的 UI 元素风格高度统一！** ✨

使用这套系统，你可以生成 100+ 个 UI 元素，它们都会：
- 色彩协调一致
- 质感风格统一
- 发光阴影协调
- 整体有"套装感"
- 可完美混搭使用

关键是：用**模板 + 固定参数 + 递进式 seed** 这三个策略！
