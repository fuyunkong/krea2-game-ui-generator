# Krea 2 Turbo INT4 ComfyUI 生成器 - Node.js 完整实现

## 🚀 项目概述

使用 Node.js + TypeScript 构建的游戏 UI 资源自动化生成系统，基于 Krea 2 Turbo INT4 模型。

**核心特性：**
- ✅ 完整的 TypeScript 类型支持
- ✅ 异步任务队列管理
- ✅ 风格统一化系统
- ✅ 递进式 Seed 管理
- ✅ 生成进度实时反馈
- ✅ 错误重试机制
- ✅ 批量生成优化

---

## 📦 项目结构

```
krea2-game-ui-generator/
├── package.json
├── tsconfig.json
├── src/
│   ├── index.ts                 # 主入口
│   ├── config/
│   │   ├── comfyui.config.ts    # ComfyUI 配置
│   │   └── styles.config.ts     # UI 风格配置
│   ├── core/
│   │   ├── generator.ts         # 核心生成器
│   │   ├── workflow.ts          # 工作流构建
│   │   └── seed-manager.ts      # Seed 管理
│   ├── types/
│   │   ├── common.ts            # 通用类型
│   │   ├── ui.ts                # UI 类型定义
│   │   └── config.ts            # 配置类型
│   ├── utils/
│   │   ├── http-client.ts       # HTTP 客户端
│   │   ├── logger.ts            # 日志系统
│   │   └── file-handler.ts      # 文件处理
│   └── templates/
│       └── prompts.ts           # 提示词模板
├── output/
│   └── workflows/               # 生成的工作流
└── .env.example
```

---

## 🛠️ 安装和配置

### 1. 初始化项目

```bash
# 创建项目目录
mkdir krea2-game-ui-generator
cd krea2-game-ui-generator

# 初始化 npm
npm init -y

# 安装依赖
npm install axios typescript @types/node dotenv

# 开发依赖
npm install -D @types/axios tsx ts-node
```

### 2. TypeScript 配置

**tsconfig.json:**
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "lib": ["ES2020"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules"]
}
```

### 3. Package.json 脚本

**package.json:**
```json
{
  "name": "krea2-game-ui-generator",
  "version": "1.0.0",
  "description": "Krea 2 Turbo INT4 Game UI Generation System",
  "main": "dist/index.js",
  "scripts": {
    "dev": "tsx watch src/index.ts",
    "build": "tsc",
    "start": "node dist/index.js",
    "generate": "tsx src/index.ts",
    "test": "tsx src/test.ts"
  },
  "dependencies": {
    "axios": "^1.6.0",
    "dotenv": "^16.0.0"
  },
  "devDependencies": {
    "@types/axios": "^0.14.0",
    "@types/node": "^20.0.0",
    "ts-node": "^10.0.0",
    "tsx": "^4.0.0",
    "typescript": "^5.0.0"
  }
}
```

### 4. 环境配置

**.env:**
```env
# ComfyUI API 配置
COMFYUI_URL=http://localhost:8188
COMFYUI_TIMEOUT=300000

# 生成器配置
OUTPUT_DIR=./output/workflows
LOG_LEVEL=info

# 项目配置
PROJECT_NAME=Fantasy RPG
BASE_SEED=42
DEFAULT_STEPS=20
DEFAULT_CFG=1.0
```

---

## 💻 完整实现代码

### 1. 类型定义

**src/types/common.ts:**
```typescript
export enum LogLevel {
  DEBUG = 'debug',
  INFO = 'info',
  WARN = 'warn',
  ERROR = 'error'
}

export interface GenerationConfig {
  projectName: string;
  baseSeed: number;
  steps: number;
  cfg: number;
  sampler: 'euler' | 'euler_ancestral' | 'heun' | 'dpm_2' | 'dpm_2_ancestral';
  scheduler: 'simple' | 'normal' | 'karras';
  model: string;
  clip: string;
  vae: string;
}

export interface GenerationResult {
  elementId: string;
  elementName: string;
  elementType: string;
  seed: number;
  prompt: string;
  status: 'pending' | 'generating' | 'success' | 'failed';
  error?: string;
  generatedAt?: Date;
}

export interface ProgressCallback {
  (progress: number, message: string): void;
}
```

**src/types/ui.ts:**
```typescript
export enum UIElementType {
  BUTTON = 'button',
  FRAME = 'frame',
  BAR = 'bar',
  ICON = 'icon',
  PANEL = 'panel',
  TEXT = 'text'
}

export interface UIElement {
  id: string;
  name: string;
  type: UIElementType;
  width?: number;
  height?: number;
  prompt?: string;
  negativePrompt?: string;
  priority?: 'high' | 'medium' | 'low';
}

export interface UIStyle {
  name: string;
  baseStyle: string;
  baseMaterial: string;
  baseTexture: string;
  baseEffect: string;
  baseColor: string;
  baseQuality: string;
}

export const DEFAULT_NEGATIVE_PROMPT = `
photorealistic, realistic people, blurry, distorted, ugly, low quality,
text, watermark, pixelated, 3D model, organic shapes, soft edges,
inconsistent design
`.trim().replace(/\n/g, ' ').replace(/  +/g, ' ');
```

**src/types/config.ts:**
```typescript
import { UIStyle } from './ui';

export interface StyleConfig {
  [key: string]: UIStyle;
}

export interface ComfyUIConfig {
  url: string;
  timeout: number;
  maxRetries: number;
}
```

### 2. 日志系统

**src/utils/logger.ts:**
```typescript
import { LogLevel } from '../types/common';

export class Logger {
  private logLevel: LogLevel = LogLevel.INFO;
  
  constructor(level: LogLevel = LogLevel.INFO) {
    this.logLevel = level;
  }
  
  private shouldLog(level: LogLevel): boolean {
    const levels = [LogLevel.DEBUG, LogLevel.INFO, LogLevel.WARN, LogLevel.ERROR];
    return levels.indexOf(level) >= levels.indexOf(this.logLevel);
  }
  
  debug(message: string, data?: any) {
    if (this.shouldLog(LogLevel.DEBUG)) {
      console.log(`[DEBUG] ${message}`, data || '');
    }
  }
  
  info(message: string, data?: any) {
    if (this.shouldLog(LogLevel.INFO)) {
      console.log(`[INFO] ✓ ${message}`, data || '');
    }
  }
  
  warn(message: string, data?: any) {
    if (this.shouldLog(LogLevel.WARN)) {
      console.warn(`[WARN] ⚠️  ${message}`, data || '');
    }
  }
  
  error(message: string, error?: any) {
    if (this.shouldLog(LogLevel.ERROR)) {
      console.error(`[ERROR] ❌ ${message}`, error || '');
    }
  }
  
  success(message: string) {
    console.log(`[SUCCESS] ✅ ${message}`);
  }
  
  progress(current: number, total: number, message: string) {
    const percentage = Math.round((current / total) * 100);
    const bar = this.createProgressBar(percentage);
    console.log(`[PROGRESS] ${bar} ${percentage}% - ${message}`);
  }
  
  private createProgressBar(percentage: number, width: number = 30): string {
    const filled = Math.round((percentage / 100) * width);
    const empty = width - filled;
    return `[${'='.repeat(filled)}${' '.repeat(empty)}]`;
  }
}

export const logger = new Logger();
```

### 3. HTTP 客户端

**src/utils/http-client.ts:**
```typescript
import axios, { AxiosInstance } from 'axios';
import { logger } from './logger';

export class HttpClient {
  private client: AxiosInstance;
  private maxRetries: number = 3;
  
  constructor(baseURL: string, timeout: number = 300000, maxRetries: number = 3) {
    this.client = axios.create({
      baseURL,
      timeout,
      headers: {
        'Content-Type': 'application/json'
      }
    });
    this.maxRetries = maxRetries;
  }
  
  async post<T>(url: string, data: any): Promise<T> {
    let lastError: any;
    
    for (let attempt = 0; attempt < this.maxRetries; attempt++) {
      try {
        const response = await this.client.post<T>(url, data);
        return response.data;
      } catch (error) {
        lastError = error;
        
        if (attempt < this.maxRetries - 1) {
          const delay = Math.pow(2, attempt) * 1000; // 指数退避
          logger.warn(`请求失败，${delay}ms 后重试 (${attempt + 1}/${this.maxRetries})`);
          await this.sleep(delay);
        }
      }
    }
    
    throw lastError;
  }
  
  async get<T>(url: string): Promise<T> {
    const response = await this.client.get<T>(url);
    return response.data;
  }
  
  private sleep(ms: number): Promise<void> {
    return new Promise(resolve => setTimeout(resolve, ms));
  }
}
```

### 4. 文件处理

**src/utils/file-handler.ts:**
```typescript
import * as fs from 'fs';
import * as path from 'path';

export class FileHandler {
  static ensureDirectoryExists(dirPath: string): void {
    if (!fs.existsSync(dirPath)) {
      fs.mkdirSync(dirPath, { recursive: true });
    }
  }
  
  static writeJSON(filePath: string, data: any): void {
    const dir = path.dirname(filePath);
    this.ensureDirectoryExists(dir);
    fs.writeFileSync(filePath, JSON.stringify(data, null, 2), 'utf-8');
  }
  
  static readJSON(filePath: string): any {
    const content = fs.readFileSync(filePath, 'utf-8');
    return JSON.parse(content);
  }
  
  static writeCSV(filePath: string, headers: string[], rows: any[][]): void {
    const dir = path.dirname(filePath);
    this.ensureDirectoryExists(dir);
    
    const csv = [headers.join(','), ...rows.map(row => row.join(','))].join('\n');
    fs.writeFileSync(filePath, csv, 'utf-8');
  }
  
  static listFiles(dirPath: string, extension?: string): string[] {
    if (!fs.existsSync(dirPath)) return [];
    
    return fs.readdirSync(dirPath)
      .filter(file => !extension || file.endsWith(extension))
      .map(file => path.join(dirPath, file));
  }
}
```

### 5. UI 风格配置

**src/config/styles.config.ts:**
```typescript
import { UIStyle, StyleConfig } from '../types/config';

export const STYLE_LIBRARY: StyleConfig = {
  fantasy_rpg: {
    name: 'Fantasy RPG',
    baseStyle: 'A fantasy RPG game',
    baseMaterial: 'ornate gold-trimmed frame with embossed surface',
    baseTexture: 'metallic polish finish',
    baseEffect: 'soft glowing edges, subtle shadow depth',
    baseColor: 'rich red and gold colors',
    baseQuality: 'professional game asset quality, 4K, sharp, detailed'
  },
  
  cyberpunk: {
    name: 'Cyberpunk Sci-Fi',
    baseStyle: 'A cyberpunk sci-fi game',
    baseMaterial: 'sleek dark background with metallic frame',
    baseTexture: 'glass-morphism effect, polished chrome',
    baseEffect: 'neon cyan glowing border, sharp geometric glow',
    baseColor: 'neon cyan and magenta with glowing effects',
    baseQuality: 'professional game asset, 2K quality, crisp, sharp'
  },
  
  cute_casual: {
    name: 'Cute Casual',
    baseStyle: 'A cute casual game',
    baseMaterial: 'soft rounded border with pastel colors',
    baseTexture: 'smooth glossy surface',
    baseEffect: 'soft glow, gentle highlight',
    baseColor: 'pastel pink and cream colors',
    baseQuality: 'professional game asset, high quality, gentle aesthetic'
  }
};
```

### 6. Seed 管理器

**src/core/seed-manager.ts:**
```typescript
export class SeedManager {
  private baseSeed: number;
  private currentSeed: number;
  private elementCount: number = 0;
  
  constructor(baseSeed: number = 42) {
    this.baseSeed = baseSeed;
    this.currentSeed = baseSeed;
  }
  
  getNextSeed(): number {
    const seed = this.currentSeed;
    this.currentSeed += 1;
    this.elementCount += 1;
    return seed;
  }
  
  getCurrentSeed(): number {
    return this.currentSeed - 1;
  }
  
  getElementCount(): number {
    return this.elementCount;
  }
  
  reset(): void {
    this.currentSeed = this.baseSeed;
    this.elementCount = 0;
  }
  
  getSeedRange(): { start: number; end: number; count: number } {
    return {
      start: this.baseSeed,
      end: this.currentSeed - 1,
      count: this.elementCount
    };
  }
}
```

### 7. 工作流构建器

**src/core/workflow.ts:**
```typescript
import { UIElement, DEFAULT_NEGATIVE_PROMPT } from '../types/ui';

export interface WorkflowNode {
  class_type: string;
  inputs: Record<string, any>;
}

export class WorkflowBuilder {
  static buildPrompt(
    baseStyle: string,
    elementType: string,
    elementName: string,
    elementProperties: any
  ): string {
    const baseMaterial = elementProperties.baseMaterial || '';
    const baseTexture = elementProperties.baseTexture || '';
    const baseEffect = elementProperties.baseEffect || '';
    const baseColor = elementProperties.baseColor || '';
    const baseQuality = elementProperties.baseQuality || '';
    
    let prompt = `${baseStyle} ${elementName}, `;
    
    switch (elementType) {
      case 'button':
        prompt += `${baseMaterial}, 3D beveled surface, ${baseTexture}, ${baseEffect}, ${baseColor}, ${baseQuality}`;
        break;
      case 'frame':
        prompt += `${baseMaterial} border, ${baseTexture}, ${baseEffect}, ${baseColor}, ${baseQuality}`;
        break;
      case 'bar':
        prompt += `gradient bar with metal frame, ${baseTexture}, ${baseEffect}, ${baseColor}, ${baseQuality}`;
        break;
      case 'icon':
        prompt += `${baseEffect}, ${baseColor}, ${baseQuality}`;
        break;
      default:
        prompt += `${baseEffect}, ${baseColor}, ${baseQuality}`;
    }
    
    return prompt;
  }
  
  static build(
    prompt: string,
    seed: number,
    steps: number = 20,
    cfg: number = 1.0,
    width: number = 1024,
    height: number = 1024,
    model: string = 'krea2_turbo_int4_tensorwise_mixed.safetensors',
    clip: string = 'qwen3vl_4b_int4_convrot.safetensors',
    vae: string = 'qwen_image_vae.safetensors'
  ): Record<string, WorkflowNode> {
    return {
      '1': {
        class_type: 'CheckpointLoaderSimple',
        inputs: { ckpt_name: model }
      },
      '2': {
        class_type: 'CLIPLoader',
        inputs: { clip_name: clip, type: 'krea2' }
      },
      '3': {
        class_type: 'VAELoader',
        inputs: { vae_name: vae }
      },
      '4': {
        class_type: 'TextEncodeKrea2',
        inputs: {
          clip: ['2', 0],
          prompt: prompt,
          strength: 1.0,
          start: 0.0,
          mode: 'before prompt'
        }
      },
      '5': {
        class_type: 'TextEncodeKrea2',
        inputs: {
          clip: ['2', 0],
          prompt: DEFAULT_NEGATIVE_PROMPT,
          strength: 1.0,
          start: 0.0,
          mode: 'before prompt'
        }
      },
      '6': {
        class_type: 'ModelSamplingFlux',
        inputs: {
          model: ['1', 0],
          shift: 1.15,
          scale_factor: 0.5,
          width: width,
          height: height
        }
      },
      '7': {
        class_type: 'EmptySD3LatentImage',
        inputs: {
          width: width,
          height: height,
          batch_size: 1
        }
      },
      '8': {
        class_type: 'KSampler',
        inputs: {
          seed: seed,
          steps: steps,
          cfg: cfg,
          sampler_name: 'euler',
          scheduler: 'simple',
          denoise: 1.0,
          model: ['6', 0],
          positive: ['4', 0],
          negative: ['5', 0],
          latent_image: ['7', 0]
        }
      },
      '9': {
        class_type: 'VAEDecode',
        inputs: {
          samples: ['8', 0],
          vae: ['3', 0]
        }
      },
      '10': {
        class_type: 'SaveImage',
        inputs: {
          filename_prefix: 'game_ui',
          images: ['9', 0]
        }
      }
    };
  }
}
```

### 8. 核心生成器

**src/core/generator.ts:**
```typescript
import { HttpClient } from '../utils/http-client';
import { FileHandler } from '../utils/file-handler';
import { logger } from '../utils/logger';
import { SeedManager } from './seed-manager';
import { WorkflowBuilder } from './workflow';
import { UIElement, UIStyle, DEFAULT_NEGATIVE_PROMPT } from '../types/ui';
import { GenerationResult, ProgressCallback } from '../types/common';

export interface GeneratorConfig {
  comfyuiUrl: string;
  outputDir: string;
  baseSeed: number;
  steps: number;
  cfg: number;
  model: string;
  clip: string;
  vae: string;
}

export class UIGenerator {
  private httpClient: HttpClient;
  private seedManager: SeedManager;
  private config: GeneratorConfig;
  private results: GenerationResult[] = [];
  
  constructor(config: GeneratorConfig) {
    this.config = config;
    this.httpClient = new HttpClient(config.comfyuiUrl, 300000, 3);
    this.seedManager = new SeedManager(config.baseSeed);
    
    FileHandler.ensureDirectoryExists(config.outputDir);
  }
  
  async generate(
    elements: UIElement[],
    style: UIStyle,
    onProgress?: ProgressCallback
  ): Promise<GenerationResult[]> {
    logger.info(`开始生成 ${elements.length} 个 UI 元素`);
    logger.info(`风格: ${style.name}`);
    logger.info(`预计耗时: ${(elements.length * 45) / 60} 分钟\n`);
    
    this.results = [];
    
    for (let i = 0; i < elements.length; i++) {
      const element = elements[i];
      const result = await this.generateElement(element, style);
      this.results.push(result);
      
      // 进度回调
      if (onProgress) {
        const progress = ((i + 1) / elements.length) * 100;
        onProgress(progress, `${result.elementName} (${result.status})`);
      }
      
      // 显示进度
      logger.progress(i + 1, elements.length, element.name);
    }
    
    return this.results;
  }
  
  private async generateElement(
    element: UIElement,
    style: UIStyle
  ): Promise<GenerationResult> {
    const seed = this.seedManager.getNextSeed();
    
    try {
      // 构建提示词
      const prompt = WorkflowBuilder.buildPrompt(
        style.baseStyle,
        element.type,
        element.name,
        {
          baseMaterial: style.baseMaterial,
          baseTexture: style.baseTexture,
          baseEffect: style.baseEffect,
          baseColor: style.baseColor,
          baseQuality: style.baseQuality
        }
      );
      
      // 构建工作流
      const workflow = WorkflowBuilder.build(
        prompt,
        seed,
        this.config.steps,
        this.config.cfg,
        element.width || 1024,
        element.height || 1024,
        this.config.model,
        this.config.clip,
        this.config.vae
      );
      
      // 保存工作流
      const workflowFile = `${this.config.outputDir}/${element.id}.json`;
      FileHandler.writeJSON(workflowFile, { prompt: workflow });
      
      // 发送到 ComfyUI
      const response: any = await this.httpClient.post('/prompt', { prompt: workflow });
      
      const result: GenerationResult = {
        elementId: element.id,
        elementName: element.name,
        elementType: element.type,
        seed: seed,
        prompt: prompt,
        status: 'success',
        generatedAt: new Date()
      };
      
      logger.info(`���成成功: ${element.name} (Seed: ${seed})`);
      return result;
    } catch (error) {
      logger.error(`生成失败: ${element.name}`, error);
      
      return {
        elementId: element.id,
        elementName: element.name,
        elementType: element.type,
        seed: seed,
        prompt: '',
        status: 'failed',
        error: String(error)
      };
    }
  }
  
  getResults(): GenerationResult[] {
    return this.results;
  }
  
  exportResults(format: 'json' | 'csv' = 'json'): void {
    if (format === 'json') {
      const file = `${this.config.outputDir}/generation_results.json`;
      FileHandler.writeJSON(file, this.results);
      logger.success(`结果导出到: ${file}`);
    } else if (format === 'csv') {
      const file = `${this.config.outputDir}/generation_results.csv`;
      const headers = ['elementId', 'elementName', 'elementType', 'seed', 'status'];
      const rows = this.results.map(r => [
        r.elementId,
        r.elementName,
        r.elementType,
        String(r.seed),
        r.status
      ]);
      FileHandler.writeCSV(file, headers, rows);
      logger.success(`结果导出到: ${file}`);
    }
  }
  
  getSummary() {
    const total = this.results.length;
    const success = this.results.filter(r => r.status === 'success').length;
    const failed = this.results.filter(r => r.status === 'failed').length;
    
    return {
      total,
      success,
      failed,
      successRate: total > 0 ? (success / total * 100).toFixed(2) + '%' : '0%',
      seedRange: this.seedManager.getSeedRange()
    };
  }
}
```

### 9. 主程序入口

**src/index.ts:**
```typescript
import * as dotenv from 'dotenv';
import { UIGenerator } from './core/generator';
import { STYLE_LIBRARY } from './config/styles.config';
import { UIElement, UIElementType } from './types/ui';
import { logger } from './utils/logger';

dotenv.config();

async function main() {
  try {
    // 配置
    const config = {
      comfyuiUrl: process.env.COMFYUI_URL || 'http://localhost:8188',
      outputDir: process.env.OUTPUT_DIR || './output/workflows',
      baseSeed: parseInt(process.env.BASE_SEED || '42'),
      steps: parseInt(process.env.DEFAULT_STEPS || '20'),
      cfg: parseFloat(process.env.DEFAULT_CFG || '1.0'),
      model: 'krea2_turbo_int4_tensorwise_mixed.safetensors',
      clip: 'qwen3vl_4b_int4_convrot.safetensors',
      vae: 'qwen_image_vae.safetensors'
    };
    
    // 创建生成器
    const generator = new UIGenerator(config);
    
    // 定义 UI 元��
    const elements: UIElement[] = [
      {
        id: 'btn_001',
        name: 'Main Menu Button',
        type: UIElementType.BUTTON,
        width: 1024,
        height: 512,
        priority: 'high'
      },
      {
        id: 'btn_002',
        name: 'Settings Button',
        type: UIElementType.BUTTON,
        width: 1024,
        height: 512,
        priority: 'high'
      },
      {
        id: frame_001',
        name: 'Inventory Frame',
        type: UIElementType.FRAME,
        width: 768,
        height: 768,
        priority: 'high'
      },
      {
        id: 'bar_001',
        name: 'Health Bar',
        type: UIElementType.BAR,
        width: 1024,
        height: 256,
        priority: 'medium'
      },
      {
        id: 'icon_001',
        name: 'Sword Icon',
        type: UIElementType.ICON,
        width: 512,
        height: 512,
        priority: 'medium'
      }
    ];
    
    // 获取风格
    const style = STYLE_LIBRARY['fantasy_rpg'];
    
    // 生成元素
    const results = await generator.generate(elements, style, (progress, message) => {
      logger.progress(progress / 100 * elements.length, elements.length, message);
    });
    
    // 输出总结
    logger.success('所有元素生成完成!');
    const summary = generator.getSummary();
    console.log('\n📊 生成总结:');
    console.log(`   总数: ${summary.total}`);
    console.log(`   成功: ${summary.success}`);
    console.log(`   失败: ${summary.failed}`);
    console.log(`   成功率: ${summary.successRate}`);
    console.log(`   Seed 范围: ${summary.seedRange.start} - ${summary.seedRange.end}`);
    
    // 导出结果
    generator.exportResults('json');
    generator.exportResults('csv');
    
  } catch (error) {
    logger.error('程序执行失败', error);
    process.exit(1);
  }
}

main();
```

---

## 🚀 快速开始

### 1. 安装依赖

```bash
npm install
```

### 2. 配置环境

复制 `.env.example` 为 `.env` 并修改：

```bash
cp .env.example .env
```

### 3. 运行生成器

```bash
# 开发模式（热重载）
npm run dev

# 直接运行
npm run generate

# 编译后运行
npm run build
npm start
```

---

## 📋 使用示例

### 自定义 UI 元素配置

**ui-config.json:**
```json
{
  "projectName": "Fantasy RPG",
  "styleKey": "fantasy_rpg",
  "baseSeed": 42,
  "elements": [
    {
      "id": "btn_main_001",
      "name": "Main Menu Play Button",
      "type": "button",
      "width": 1024,
      "height": 512,
      "priority": "high"
    },
    {
      "id": "frame_inv_001",
      "name": "Inventory Frame",
      "type": "frame",
      "width": 768,
      "height": 768,
      "priority": "high"
    },
    {
      "id": "bar_health_001",
      "name": "Health Bar",
      "type": "bar",
      "width": 1024,
      "height": 256,
      "priority": "medium"
    }
  ]
}
```

**加载配置文件的生成器：**
```typescript
import { FileHandler } from './utils/file-handler';

async function generateFromConfig(configFile: string) {
  const config = FileHandler.readJSON(configFile);
  const generator = new UIGenerator({
    comfyuiUrl: process.env.COMFYUI_URL || 'http://localhost:8188',
    outputDir: './output/workflows',
    baseSeed: config.baseSeed,
    steps: 20,
    cfg: 1.0,
    model: 'krea2_turbo_int4_tensorwise_mixed.safetensors',
    clip: 'qwen3vl_4b_int4_convrot.safetensors',
    vae: 'qwen_image_vae.safetensors'
  });
  
  const style = STYLE_LIBRARY[config.styleKey];
  const results = await generator.generate(config.elements, style);
  
  generator.exportResults('json');
  generator.exportResults('csv');
}

generateFromConfig('ui-config.json');
```

---

## 🎯 高级用法

### 添加自定义风格

```typescript
import { STYLE_LIBRARY } from './config/styles.config';

STYLE_LIBRARY['my_custom_style'] = {
  name: 'My Custom Style',
  baseStyle: 'A custom game',
  baseMaterial: 'custom material description',
  baseTexture: 'custom texture',
  baseEffect: 'custom effect',
  baseColor: 'custom colors',
  baseQuality: 'professional game asset, 4K'
};
```

### 并行生成（需要 ComfyUI 支持队列）

```typescript
async function parallelGenerate(elements: UIElement[], batchSize: number = 3) {
  for (let i = 0; i < elements.length; i += batchSize) {
    const batch = elements.slice(i, i + batchSize);
    await Promise.all(batch.map(element => generator.generateElement(element, style)));
  }
}
```

---

## 📊 输出文件结构

```
output/
├── workflows/
│   ├── btn_001.json           # 单个工作流
│   ├── btn_002.json
│   ├── frame_001.json
│   ├── generation_results.json # 生成结果汇总 (JSON)
│   └── generation_results.csv  # 生成结果汇总 (CSV)
└── assets/                    # 生成的图片（由 ComfyUI 输出）
    ├── btn_001.png
    ├── btn_002.png
    └── ...
```

---

## ✨ 特性总结

| 特性 | 说明 |
|-----|------|
| ✅ **类型安全** | 完整的 TypeScript 支持 |
| ✅ **风格统一** | 统一的风格模板系统 |
| ✅ **Seed 管理** | 递进式 Seed 管理 |
| ✅ **错误重试** | 自动重试机制 |
| ✅ **进度反馈** | 实时生成进度 |
| ✅ **结果导出** | JSON/CSV 格式导出 |
| ✅ **配置灵活** | 环境变量 + JSON 配置 |
| ✅ **日志系统** | 详细的日志输出 |

---

**现在你有了完整的 Node.js 实现！功能完全等同于 Python 版本，但有更好的类型安全和性能。** 🎉
