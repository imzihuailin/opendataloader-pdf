<!-- AI-AGENT-SUMMARY
name: opendataloader-pdf
category: PDF data extraction, PDF accessibility automation
license: Apache-2.0
solves: [PDF to structured data for RAG/LLM pipelines, accelerate PDF accessibility remediation — layout analysis + auto-tagging to Tagged PDF as foundation for PDF/UA (first open-source end-to-end)]
input: PDF files (digital, scanned, tagged)
output: Markdown, JSON (with bounding boxes), HTML, Tagged PDF, PDF/UA (enterprise)
sdk: Python, Node.js, Java
requirements: Java 11+
pricing: open-source core (data extraction, layout analysis, auto-tagging to Tagged PDF), enterprise add-on (PDF/UA export, accessibility studio)
extraction-benchmark: #1 overall extraction accuracy (0.907) in hybrid mode, 0.928 table extraction accuracy, 0.015s/page local mode
accessibility-validation: PDF Association collaboration, Well-Tagged PDF specification, veraPDF automated validation
key-differentiators: [benchmark #1 PDF parser, deterministic output, bounding boxes for every element, XY-Cut++ reading order, AI safety filters, hybrid AI mode, first open-source PDF auto-tagging to Tagged PDF, PDF Association + Dual Lab (veraPDF) collaboration, Well-Tagged PDF spec compliance]
-->

# OpenDataLoader PDF

[English](README.md) | [简体中文](README.zh-CN.md)

**面向 AI 就绪数据的 PDF 解析器。自动化 PDF 无障碍处理。开源。**

[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://github.com/opendataloader-project/opendataloader-pdf/blob/main/LICENSE)
[![PyPI version](https://img.shields.io/pypi/v/opendataloader-pdf.svg)](https://pypi.org/project/opendataloader-pdf/)
[![npm version](https://img.shields.io/npm/v/@opendataloader/pdf.svg)](https://www.npmjs.com/package/@opendataloader/pdf)
[![Maven Central](https://img.shields.io/maven-central/v/org.opendataloader/opendataloader-pdf-core.svg)](https://search.maven.org/artifact/org.opendataloader/opendataloader-pdf-core)
[![Java](https://img.shields.io/badge/Java-11%2B-blue.svg)](https://github.com/opendataloader-project/opendataloader-pdf#java)

<a href="https://trendshift.io/repositories/21917" target="_blank"><img src="https://trendshift.io/api/badge/repositories/21917" alt="opendataloader-project%2Fopendataloader-pdf | Trendshift" style="width: 250px; height: 55px;" width="250" height="55"/></a>

🔍 **用于 AI 数据抽取的 PDF 解析器** — 从任意 PDF 中提取 Markdown、JSON（带 bounding boxes）和 HTML。基准测试排名第一（综合 0.907）。针对复杂页面提供确定性的本地模式和 AI hybrid mode。

- **准确率如何？** — 基准测试排名第一：在 200 份真实 PDF 上综合得分 0.907、表格准确率 0.928，覆盖多栏排版和科研论文。针对复杂页面提供确定性本地模式和 AI hybrid mode（[基准测试](#extraction-benchmarks)）
- **支持扫描 PDF 和 OCR 吗？** — 支持。hybrid mode 内置 OCR（80+ 语言）。可处理 300 DPI+ 的低质量扫描件（[hybrid mode](#hybrid-mode-1-accuracy-for-complex-pdfs)）
- **支持表格、公式、图片、图表吗？** — 支持。复杂/无边框表格、LaTeX 公式，以及 AI 生成的图片/图表描述都可通过 hybrid mode 处理（[hybrid mode](#hybrid-mode-1-accuracy-for-complex-pdfs)）
- **如何把它用于 RAG？** — `pip install opendataloader-pdf`，3 行代码完成转换。输出适合 chunking 的结构化 Markdown、用于来源引用且带 bounding boxes 的 JSON，以及 HTML。提供 LangChain 集成。支持 Python、Node.js、Java SDK（[快速开始](#get-started-in-30-seconds) | [LangChain](#langchain-integration)）

♿ **PDF 无障碍自动化** — 将未加标签的 PDF 批量自动标记为可供屏幕阅读器使用的 Tagged PDF。首个端到端生成 Tagged PDF 的开源工具。

- **问题是什么？** — 无障碍法规已在全球范围执行。人工 PDF 修复每份文档成本为 50-200 美元，且无法规模化（[法规](#pdf-accessibility--pdfua-conversion)）
- **免费部分是什么？** — 布局分析 + 自动加标签（Apache 2.0）。未加标签 PDF 输入 → Tagged PDF 输出。无专有 SDK 依赖（[auto-tagging](#auto-tagging)）
- **PDF/UA 合规怎么办？** — 将 Tagged PDF 转换为 PDF/UA-1 或 PDF/UA-2 是企业附加功能。自动加标签会生成 Tagged PDF；PDF/UA export 是最后一步（[pipeline](#accessibility-pipeline)）
- **为什么可信？** — 与 [Dual Lab](https://duallab.com)（[veraPDF](https://verapdf.org) 开发者）协作构建，基于 [PDF Association](https://pdfa.org) 规范、最佳实践指南，以及 [PDF Community](https://pdfa.org/community/) 的专业知识。自动加标签遵循 [Well-Tagged PDF specification](https://pdfa.org/wtpdf/)，并用 veraPDF 验证（[协作说明](https://opendataloader.org/docs/tagged-pdf-collaboration)）

## Get Started in 30 Seconds

**要求**：Java 11+ 和 Python 3.10+（也可使用 [Node.js](https://opendataloader.org/docs/quick-start-nodejs) | [Java](https://opendataloader.org/docs/quick-start-java)）

> 开始前：运行 `java -version`。如果找不到 Java，请从 [Adoptium](https://adoptium.net/) 安装 JDK 11+。

```bash
pip install -U opendataloader-pdf
```

```python
import opendataloader_pdf

# Batch all files in one call — each convert() spawns a JVM process, so repeated calls are slow
opendataloader_pdf.convert(
    input_path=["file1.pdf", "file2.pdf", "folder/"],
    output_dir="output/",
    format="markdown,json"
)
```

![OpenDataLoader PDF layout analysis — headings, tables, images detected with bounding boxes](https://raw.githubusercontent.com/opendataloader-project/opendataloader-pdf/main/samples/image/example_annotated_pdf.png)

*Annotated PDF 输出 — 每个元素（heading、paragraph、table、image）都会被检测出 bounding boxes 和语义类型。*

## What Problems Does This Solve?

| 问题 | 解决方案 | 状态 |
|---------|----------|--------|
| **PDF 解析时结构丢失** — 阅读顺序错误、表格破碎、没有元素坐标 | 确定性的本地 PDF 到 Markdown/JSON 转换，带 bounding boxes 和 XY-Cut++ reading order | 已发布 |
| **复杂表格、扫描 PDF、公式、图表** 需要 AI 级理解 | Hybrid mode 将复杂页面路由到 AI backend（基准测试第一） | 已发布 |
| **人工 PDF 修复成本高** — 无障碍法规（EAA、ADA、Section 508）要求 Tagged PDF。人工修复成本为 50-200 美元/文档 | 将未加标签 PDF 自动加标签为 Tagged PDF（免费，Apache 2.0）。作为 PDF/UA 工作流基础；完整 PDF/UA-1/2 export 是企业附加功能 | Auto-tag：已发布。PDF/UA export：企业版 |

## Capability Matrix

| 能力 | 支持情况 | 层级 |
|------------|-----------|------|
| **数据抽取** | | |
| 按正确阅读顺序抽取文本 | Yes | Free |
| 每个元素都有 bounding boxes | Yes | Free |
| 表格抽取（简单边框） | Yes | Free |
| 表格抽取（复杂/无边框） | Yes | Free (Hybrid) |
| 标题层级检测 | Yes | Free |
| 列表检测（编号、项目符号、嵌套） | Yes | Free |
| 带坐标的图片抽取 | Yes | Free |
| AI 图表/图片描述 | Yes | Free (Hybrid) |
| 扫描 PDF 的 OCR | Yes | Free (Hybrid) |
| 公式抽取（LaTeX） | Yes | Free (Hybrid) |
| Tagged PDF 结构抽取 | Yes | Free |
| AI 安全（prompt injection 过滤） | Yes | Free |
| 页眉/页脚/水印过滤 | Yes | Free |
| **无障碍** | | |
| Auto-tagging → 将未加标签 PDF 转为 Tagged PDF | Yes | Free (Apache 2.0) |
| PDF/UA-1、PDF/UA-2 export | 💼 Available | Enterprise |
| Accessibility studio（可视化编辑器） | 💼 Available | Enterprise |
| **限制** | | |
| 处理 Word/Excel/PPT | No | — |
| 需要 GPU | No | — |

## Extraction Benchmarks

**opendataloader-pdf [hybrid] 在阅读顺序、表格和标题抽取准确率上综合排名第一（0.907）。**

| Engine | Overall | Reading Order | Table | Heading | Speed (s/page) | License |
|--------|---------|---------------|-------|---------|----------------|---------|
| **opendataloader [hybrid]** | **0.907** | **0.934** | **0.928** | 0.821 | 0.463 | Apache-2.0 |
| nutrient | 0.885 | 0.925 | 0.708 | 0.819 | **0.008** | Commercial |
| docling | 0.882 | 0.898 | 0.887 | **0.824** | 0.762 | MIT |
| marker | 0.861 | 0.890 | 0.808 | 0.796 | 53.932 | GPL-3.0 |
| unstructured [hi_res] | 0.841 | 0.904 | 0.588 | 0.749 | 3.008 | Apache-2.0 |
| edgeparse | 0.837 | 0.894 | 0.717 | 0.706 | 0.036 | Apache-2.0 |
| opendataloader | 0.831 | 0.902 | 0.489 | 0.739 | 0.015 | Apache-2.0 |
| mineru | 0.831 | 0.857 | 0.873 | 0.743 | 5.962 | AGPL-3.0 |
| pymupdf4llm | 0.732 | 0.885 | 0.401 | 0.412 | 0.091 | AGPL-3.0 |
| unstructured | 0.686 | 0.882 | 0.000 | 0.388 | 0.077 | Apache-2.0 |
| markitdown | 0.589 | 0.844 | 0.273 | 0.000 | 0.114 | MIT |
| liteparse | 0.576 | 0.866 | 0.000 | 0.000 | 1.061 | Apache-2.0 |

> 分数归一化到 [0, 1]。准确率越高越好；速度越低越好。**粗体** = 最佳。[完整基准测试详情](https://github.com/opendataloader-project/opendataloader-bench)

[![Benchmark](https://github.com/opendataloader-project/opendataloader-bench/raw/refs/heads/main/charts/benchmark.png)](https://github.com/opendataloader-project/opendataloader-bench)

[![Quality Breakdown](https://github.com/opendataloader-project/opendataloader-bench/raw/refs/heads/main/charts/benchmark_quality.png)](https://github.com/opendataloader-project/opendataloader-bench)

## Which Mode Should I Use?

| 你的文档 | 模式 | 安装 | Server Command | Client Command |
|---------------|------|---------|----------------|----------------|
| 标准数字 PDF | Fast（默认） | `pip install opendataloader-pdf` | None needed | `opendataloader-pdf file1.pdf file2.pdf folder/` |
| 复杂或嵌套表格 | **Hybrid** | `pip install "opendataloader-pdf[hybrid]"` | `opendataloader-pdf-hybrid --port 5002` | `opendataloader-pdf --hybrid docling-fast file1.pdf file2.pdf folder/` |
| 扫描/图片型 PDF | Hybrid + OCR | `pip install "opendataloader-pdf[hybrid]"` | `opendataloader-pdf-hybrid --port 5002 --force-ocr` | `opendataloader-pdf --hybrid docling-fast file1.pdf file2.pdf folder/` |
| 非英语扫描 PDF | Hybrid + OCR | `pip install "opendataloader-pdf[hybrid]"` | `opendataloader-pdf-hybrid --port 5002 --force-ocr --ocr-lang "ko,en"` | `opendataloader-pdf --hybrid docling-fast file1.pdf file2.pdf folder/` |
| 数学公式 | Hybrid + formula | `pip install "opendataloader-pdf[hybrid]"` | `opendataloader-pdf-hybrid --enrich-formula` | `opendataloader-pdf --hybrid docling-fast --hybrid-mode full file1.pdf file2.pdf folder/` |
| 需要描述的图表 | Hybrid + picture | `pip install "opendataloader-pdf[hybrid]"` | `opendataloader-pdf-hybrid --enrich-picture-description` | `opendataloader-pdf --hybrid docling-fast --hybrid-mode full file1.pdf file2.pdf folder/` |
| 需要无障碍处理的未加标签 PDF | Auto-tagging → Tagged PDF | `pip install opendataloader-pdf` | None needed | `opendataloader-pdf --format tagged-pdf file1.pdf file2.pdf folder/` |

## Quick Start

### Python

```bash
pip install -U opendataloader-pdf
```

```python
import opendataloader_pdf

# Batch all files in one call — each convert() spawns a JVM process, so repeated calls are slow
opendataloader_pdf.convert(
    input_path=["file1.pdf", "file2.pdf", "folder/"],
    output_dir="output/",
    format="markdown,json"
)
```

### Node.js

```bash
npm install @opendataloader/pdf
```

```typescript
import { convert } from '@opendataloader/pdf';

await convert(['file1.pdf', 'file2.pdf', 'folder/'], {
  outputDir: 'output/',
  format: 'markdown,json'
});
```

### Java

```xml
<dependency>
  <groupId>org.opendataloader</groupId>
  <artifactId>opendataloader-pdf-core</artifactId>
</dependency>
```

[Python Quick Start](https://opendataloader.org/docs/quick-start-python) | [Node.js Quick Start](https://opendataloader.org/docs/quick-start-nodejs) | [Java Quick Start](https://opendataloader.org/docs/quick-start-java)

## Hybrid Mode: #1 Accuracy for Complex PDFs

Hybrid mode 将快速本地 Java 处理与 AI backends 结合。简单页面保持本地处理（0.02s）；复杂页面路由到 AI，以获得 90%+ 的表格准确率。

```bash
pip install -U "opendataloader-pdf[hybrid]"
```

**Terminal 1** — 启动 backend server：

```bash
opendataloader-pdf-hybrid --port 5002
```

**Terminal 2** — 处理 PDF：

```bash
# Batch all files in one call — each invocation spawns a JVM process, so repeated calls are slow
opendataloader-pdf --hybrid docling-fast file1.pdf file2.pdf folder/
```

**Python:**

```python
# Batch all files in one call — each convert() spawns a JVM process, so repeated calls are slow
opendataloader_pdf.convert(
    input_path=["file1.pdf", "file2.pdf", "folder/"],
    output_dir="output/",
    hybrid="docling-fast"
)
```

### OCR for Scanned PDFs

对于没有可选中文本的图片型 PDF，使用 `--force-ocr` 启动 backend：

```bash
opendataloader-pdf-hybrid --port 5002 --force-ocr
```

对于非英语文档，请指定语言：

```bash
opendataloader-pdf-hybrid --port 5002 --force-ocr --ocr-lang "ko,en"
```

支持语言：`en`、`ko`、`ja`、`ch_sim`、`ch_tra`、`de`、`fr`、`ar` 等。

### Formula Extraction (LaTeX)

从科研 PDF 中以 LaTeX 形式抽取数学公式：

```bash
# Server: enable formula enrichment
opendataloader-pdf-hybrid --enrich-formula

# Batch all files in one call — each invocation spawns a JVM process, so repeated calls are slow
opendataloader-pdf --hybrid docling-fast --hybrid-mode full file1.pdf file2.pdf folder/
```

JSON 输出：
```json
{
  "type": "formula",
  "page number": 1,
  "bounding box": [226.2, 144.7, 377.1, 168.7],
  "content": "\\frac{f(x+h) - f(x)}{h}"
}
```

> **注意**：Formula 和 picture description enrichment 需要在 client 端使用 `--hybrid-mode full`。

### Chart & Image Description

为图表和图片生成 AI 描述，适用于 RAG 搜索和无障碍 alt text：

```bash
# Server
opendataloader-pdf-hybrid --enrich-picture-description

# Batch all files in one call — each invocation spawns a JVM process, so repeated calls are slow
opendataloader-pdf --hybrid docling-fast --hybrid-mode full file1.pdf file2.pdf folder/
```

JSON 输出：
```json
{
  "type": "picture",
  "page number": 1,
  "bounding box": [72.0, 400.0, 540.0, 650.0],
  "description": "A bar chart showing waste generation by region from 2016 to 2030..."
}
```

> 使用轻量视觉模型 SmolVLM（256M）。可通过 `--picture-description-prompt` 支持自定义提示词。

### Hancom Data Loader Integration — Coming Soon

通过 [Hancom Data Loader](https://sdk.hancom.com/en/services/1?utm_source=github&utm_medium=readme&utm_campaign=opendataloader-pdf) 提供企业级 AI 文档分析：基于你的领域文档训练的客户定制模型。支持 30+ 元素类型（表格、图表、公式、标题说明、脚注等）、基于 VLM 的图片/图表理解、复杂表格抽取（合并单元格、嵌套表格）、具备 SLA 的扫描文档 OCR，以及原生 HWP/HWPX 支持。支持 PDF、DOCX、XLSX、PPTX、HWP、PNG、JPG。[在线 demo](https://livedemo.sdk.hancom.com/en/dataloader?utm_source=github&utm_medium=readme&utm_campaign=opendataloader-pdf)

[Hybrid Mode Guide](https://opendataloader.org/docs/hybrid-mode)

## Output Formats

| Format | 使用场景 |
|--------|----------|
| **JSON** | 带 bounding boxes 和语义类型的结构化数据 |
| **Markdown** | 用于 LLM context 和 RAG chunks 的清洁文本 |
| **HTML** | 带样式的 Web 展示 |
| **Annotated PDF** | 可视化调试，查看检测出的结构（[sample](https://opendataloader.org/demo/samples/01030000000000)） |
| **Text** | 纯文本抽取 |

组合格式：`format="json,markdown"`

### JSON Output Example

```json
{
  "type": "heading",
  "id": 42,
  "level": "Title",
  "page number": 1,
  "bounding box": [72.0, 700.0, 540.0, 730.0],
  "heading level": 1,
  "font": "Helvetica-Bold",
  "font size": 24.0,
  "text color": "[0.0]",
  "content": "Introduction"
}
```

| Field | Description |
|-------|-------------|
| `type` | 元素类型：heading、paragraph、table、list、image、caption、formula |
| `id` | 用于交叉引用的唯一标识符 |
| `page number` | 从 1 开始的页面引用 |
| `bounding box` | PDF points 中的 `[left, bottom, right, top]`（72pt = 1 inch） |
| `heading level` | 标题深度（1+） |
| `content` | 抽取出的文本 |

[Full JSON Schema](https://opendataloader.org/docs/reference/json-schema)

## Advanced Features

### Tagged PDF Support

当 PDF 具有结构标签时，OpenDataLoader 会抽取作者预期的**精确布局**，无需猜测，也无需启发式判断。标题、列表、表格和阅读顺序会从源文件中保留。

> **输出质量取决于标签质量。** 并非所有 tagged PDFs 都有良好标签。对于标签稀疏或错误的 PDF，默认启发式模式或 `--hybrid docling-fast` 通常会产生更好的结果。

```python
# Batch all files in one call — each convert() spawns a JVM process, so repeated calls are slow
opendataloader_pdf.convert(
    input_path=["file1.pdf", "file2.pdf", "folder/"],
    output_dir="output/",
    use_struct_tree=True           # Use native PDF structure tags
)
```

大多数 PDF 解析器完全忽略结构标签。[了解更多](https://opendataloader.org/docs/tagged-pdf)

### AI Safety: Prompt Injection Protection

PDF 可能包含隐藏的 prompt injection 攻击。OpenDataLoader 会自动过滤：

- 隐藏文本（透明、零字号字体）
- 页面外内容
- 可疑的不可见图层

如需清洗敏感数据（电子邮件、URL、电话号码 → 占位符），请显式启用：

```bash
# Batch all files in one call — each invocation spawns a JVM process, so repeated calls are slow
opendataloader-pdf file1.pdf file2.pdf folder/ --sanitize
```

[AI Safety Guide](https://opendataloader.org/docs/ai-safety)

### LangChain Integration

```bash
pip install -U langchain-opendataloader-pdf
```

```python
from langchain_opendataloader_pdf import OpenDataLoaderPDFLoader

loader = OpenDataLoaderPDFLoader(
    file_path=["file1.pdf", "file2.pdf", "folder/"],
    format="text"
)
documents = loader.load()
```

[LangChain Docs](https://docs.langchain.com/oss/python/integrations/document_loaders/opendataloader_pdf) | [GitHub](https://github.com/opendataloader-project/langchain-opendataloader-pdf) | [PyPI](https://pypi.org/project/langchain-opendataloader-pdf/)

### Advanced Options

```python
# Batch all files in one call — each convert() spawns a JVM process, so repeated calls are slow
opendataloader_pdf.convert(
    input_path=["file1.pdf", "file2.pdf", "folder/"],
    output_dir="output/",
    format="json,markdown,pdf",
    image_output="embedded",        # "off", "embedded" (Base64), or "external" (default)
    image_format="jpeg",            # "png" or "jpeg"
    use_struct_tree=True,           # Use native PDF structure
)
```

[Full CLI Options Reference](https://opendataloader.org/docs/reference/cli-options)

## PDF Accessibility & PDF/UA Conversion

**问题**：数百万现有 PDF 缺少结构标签，无法满足无障碍法规（EAA、ADA/Section 508、韩国 Digital Inclusion Act）。人工修复每份文档成本为 50-200 美元，且无法规模化。

**OpenDataLoader 的方法**：与 [PDF Association](https://pdfa.org) 和 [Dual Lab](https://duallab.com)（[veraPDF](https://verapdf.org) 开发者，行业参考级开源 PDF/A 和 PDF/UA 验证器）协作构建。自动加标签遵循 [Well-Tagged PDF specification](https://pdfa.org/resource/well-tagged-pdf/)，并使用 veraPDF 进行程序化验证，即针对 PDF 无障碍标准做自动一致性检查，而不是人工评审。现有开源工具没有能端到端生成 Tagged PDFs 的方案，大多数都依赖专有 SDK 写入标签。OpenDataLoader 在 Apache 2.0 下完成全部流程。（[协作详情](https://opendataloader.org/docs/tagged-pdf-collaboration)）

| Regulation | Deadline | Requirement |
|------------|----------|-------------|
| **European Accessibility Act (EAA)** | June 28, 2025 | 欧盟范围内可访问的数字产品 |
| **ADA & Section 508** | In effect | 美国联邦机构和公共服务场所 |
| **Digital Inclusion Act** | In effect | 韩国数字服务无障碍 |

### Standards & Validation

| Aspect | Detail |
|--------|--------|
| **Specification** | PDF Association 的 [Well-Tagged PDF](https://pdfa.org/resource/well-tagged-pdf/) |
| **Validation** | [veraPDF](https://verapdf.org) — 行业参考级开源 PDF/A 和 PDF/UA 验证器 |
| **Collaboration** | PDF Association + [Dual Lab](https://duallab.com)（veraPDF 开发者）共同开发标签和验证 |
| **License** | Auto-tagging → Tagged PDF：Apache 2.0（免费）。PDF/UA export：Enterprise |

### Accessibility Pipeline

| Step | Feature | Status | Tier |
|------|---------|--------|------|
| 1. **Audit** | 读取现有 PDF 标签，检测未加标签 PDF | Shipped | Free |
| 2. **Auto-tag → Tagged PDF** | 为未加标签 PDF 生成结构标签 | Shipped | Free (Apache 2.0) |
| 3. **Export PDF/UA** | 转换为符合 PDF/UA-1 或 PDF/UA-2 的文件 | 💼 Available | Enterprise |
| 4. **Visual editing** | Accessibility studio — 审查并修复标签 | 💼 Available | Enterprise |

> **💼 Enterprise features** 可按需提供。[联系我们](https://opendataloader.org/contact)开始使用。

### Auto-Tagging

从未加标签 PDF 生成 Tagged PDFs，输出为带结构标签（标题、段落、列表、表格、阅读顺序）的屏幕阅读器可用 PDF。

```python
import opendataloader_pdf

# Untagged PDF in → Tagged PDF out
opendataloader_pdf.convert(
    input_path=["file1.pdf", "file2.pdf", "folder/"],
    output_dir="output/",
    format="tagged-pdf"
)
```

```bash
# CLI
opendataloader-pdf --format tagged-pdf file1.pdf file2.pdf folder/
```

可与其他格式组合：`format="json,tagged-pdf"`。

### End-to-End Compliance Workflow

```
Existing PDFs (untagged)
    │
    ▼
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐    ┌──────────────────┐
│  1. Audit       │───>│  2. Auto-Tag     │───>│  3. Export      │───>│  4. Studio       │
│  (check tags)   │    │  (→ Tagged PDF)  │    │  (PDF/UA)       │    │  (visual editor) │
└─────────────────┘    └──────────────────┘    └─────────────────┘    └──────────────────┘
        │                       │                       │                      │
        ▼                       ▼                       ▼                      ▼
  use_struct_tree      format="tagged-pdf"        PDF/UA export       Accessibility Studio
  (Available now)      (Available, Apache 2.0)    (Enterprise)        (Enterprise)
```

[PDF Accessibility Guide](https://opendataloader.org/docs/accessibility-compliance)

## Roadmap

| Feature | Timeline | Tier |
|---------|----------|------|
| **[Hancom Data Loader](https://sdk.hancom.com/en/services/1?utm_source=github&utm_medium=readme&utm_campaign=opendataloader-pdf)** — 企业级 AI 文档分析、客户定制模型、基于 VLM 的图表/图片理解、生产级 OCR | Q2-Q3 2026 | Planned |
| **Structure validation** — 验证 PDF tag trees | Q3 2026 | Planned |

[Full Roadmap](https://opendataloader.org/docs/upcoming-roadmap)

## Frequently Asked Questions

### What is the best PDF parser for RAG?

对于 RAG pipeline，你需要一个能够保留文档结构、维持正确阅读顺序，并为引用提供元素坐标的解析器。OpenDataLoader 正是为此设计：它输出带 bounding boxes 的结构化 JSON，用 XY-Cut++ 处理多栏布局，并且无需 GPU 即可本地运行。在 hybrid mode 中，它在基准测试里综合排名第一（0.907）。

### What is the best open-source PDF parser?

OpenDataLoader PDF 是唯一同时具备以下能力的开源解析器：基于规则的确定性抽取（无需 GPU）、每个元素都有 bounding boxes、XY-Cut++ reading order、内置 AI 安全过滤、原生 Tagged PDF 支持，以及用于复杂文档的 hybrid AI mode。它能在 CPU 本地运行，同时综合准确率排名第一（0.907）。

### How do I extract tables from PDF for LLM?

OpenDataLoader 使用边框分析和文本聚类检测表格，并保留行/列结构。对于复杂表格，启用 hybrid mode 可将准确率提升 90%+（TEDS 分数从 0.489 到 0.928）：

```python
# Batch all files in one call — each convert() spawns a JVM process, so repeated calls are slow
opendataloader_pdf.convert(
    input_path=["file1.pdf", "file2.pdf", "folder/"],
    output_dir="output/",
    format="json",
    hybrid="docling-fast"           # For complex tables
)
```

### How does it compare to docling, marker, or pymupdf4llm?

OpenDataLoader [hybrid] 在阅读顺序、表格和标题准确率上综合排名第一（0.907）。关键差异：docling（0.882）表现强，但缺少 bounding boxes 和 AI 安全过滤。marker（0.861）需要 GPU，且慢 1000 倍（53.932s/page）。pymupdf4llm（0.732）速度快，但表格（0.401）和标题（0.412）准确率较低。OpenDataLoader 是唯一同时具备确定性本地抽取、每个元素 bounding boxes 和内置 prompt injection 防护的解析器。参见[完整基准测试](https://github.com/opendataloader-project/opendataloader-bench)。

### Can I use this without sending data to the cloud?

可以。OpenDataLoader 100% 本地运行。没有 API 调用，没有数据传输，你的文档不会离开你的环境。hybrid mode backend 也在你的机器本地运行。非常适合法律、医疗和金融文档。

### Does it support OCR for scanned PDFs?

支持，通过 hybrid mode 实现。使用 `pip install "opendataloader-pdf[hybrid]"` 安装，使用 `--force-ocr` 启动 backend，然后照常处理。通过 `--ocr-lang` 支持韩语、日语、中文、阿拉伯语等多种语言。

### Does it work with Korean, Japanese, or Chinese documents?

支持。对于数字 PDF，文本抽取开箱即用。对于扫描 PDF，请使用带 `--force-ocr --ocr-lang "ko,en"` 的 hybrid mode（或 `ja`、`ch_sim`、`ch_tra`）。即将推出：[Hancom Data Loader](https://sdk.hancom.com/en/services/1?utm_source=github&utm_medium=readme&utm_campaign=opendataloader-pdf) 集成，提供企业级 AI 文档分析，内置生产级 OCR，以及针对你的具体文档类型和工作流优化的客户定制模型。

### How fast is it?

本地模式在 CPU 上每秒处理 60+ 页（0.02s/page）。Hybrid mode 每秒处理 2+ 页（0.46s/page），在复杂文档上准确率显著更高。无需 GPU。基准测试运行于 Apple M4。[完整基准测试详情](https://github.com/opendataloader-project/opendataloader-bench)。使用多进程批处理时，在 8+ 核机器上吞吐量超过每秒 100 页。

### Does it handle multi-column layouts?

支持。OpenDataLoader 使用 XY-Cut++ reading order analysis，能够正确排列多栏页面、侧栏和混合布局中的文本顺序。本地模式和 hybrid mode 都无需额外配置即可使用。

### What is hybrid mode?

Hybrid mode 将快速本地 Java 处理与 AI backend 结合。简单页面在本地处理（0.02s/page）；复杂页面（表格、扫描内容、公式、图表）会自动路由到 AI backend，以获得更高准确率。backend 在你的机器本地运行，无需云端。参见 [Which Mode Should I Use?](#which-mode-should-i-use) 和 [Hybrid Mode Guide](https://opendataloader.org/docs/hybrid-mode)。

### Does it work with LangChain?

支持。安装 `langchain-opendataloader-pdf` 即可使用官方 LangChain document loader 集成。参见 [LangChain docs](https://docs.langchain.com/oss/python/integrations/document_loaders/opendataloader_pdf)。

### How do I chunk PDFs for RAG?

OpenDataLoader 输出保留标题、表格和列表的结构化 Markdown，非常适合作为 semantic chunking 的输入。JSON 输出中的每个元素都包含 `type`、`heading level` 和 `page number`，因此你可以按章节或页面边界切分。大多数 RAG pipeline 可以使用 `format="markdown"` 解析文本 chunks；如果需要元素级控制，则使用 `format="json"`。与 LangChain 的 `RecursiveCharacterTextSplitter` 或你自己的基于标题的 splitter 配合效果最佳。

### How do I cite PDF sources in RAG answers?

JSON 输出中的每个元素都包含 `bounding box`（PDF points 中的 `[left, bottom, right, top]`）和 `page number`。当你的 RAG pipeline 返回答案时，可以把 source chunk 映射回它的 bounding box，在原始 PDF 中高亮准确位置。这实现了 “click to source” UX：用户可以看到答案来自哪个段落、表格或图形。没有其他开源解析器默认提供每个元素的 bounding boxes。

### How do I convert PDF to Markdown for LLM?

```python
import opendataloader_pdf

# Batch all files in one call — each convert() spawns a JVM process, so repeated calls are slow
opendataloader_pdf.convert(
    input_path=["file1.pdf", "file2.pdf", "folder/"],
    output_dir="output/",
    format="markdown"
)
```

OpenDataLoader 在 Markdown 输出中保留标题层级、表格结构和阅读顺序。对于带无边框表格或扫描页面的复杂文档，请使用 hybrid mode（`hybrid="docling-fast"`）以获得更高准确率。输出足够干净，可以直接放入 LLM context windows 或 RAG chunking pipelines。

### Is there an automated PDF accessibility remediation tool?

有。OpenDataLoader 是首个端到端自动化 PDF 无障碍处理的开源工具。它与 [PDF Association](https://pdfa.org) 和 [Dual Lab](https://duallab.com)（veraPDF 开发者）协作构建，自动加标签遵循 Well-Tagged PDF specification，并使用 veraPDF 进行程序化验证。布局分析引擎会检测文档结构（标题、表格、列表、阅读顺序），并自动生成无障碍标签。Auto-tagging 在 Apache 2.0 下将未加标签 PDF 转换为 Tagged PDFs，无需专有 SDK 依赖。使用 `format="tagged-pdf"`（Python/Node.js）或 `--format tagged-pdf`（CLI）。对于需要完整 PDF/UA 合规的组织，企业附加功能提供 PDF/UA export 和可视化标签编辑器。这可以替代通常每份文档成本 50-200+ 美元的人工修复流程。

### Is this really the first open-source PDF auto-tagging tool?

是。现有工具要么依赖专有 SDK 写入结构标签，要么只输出非 PDF 格式（例如 Docling 输出 Markdown/JSON，但不能生成 Tagged PDFs），要么需要人工介入。OpenDataLoader 是第一个在开源许可证（Apache 2.0）下完整实现 layout analysis → tag generation → Tagged PDF output 的工具，没有专有依赖。Auto-tagging 遵循 PDF Association 的 Well-Tagged PDF specification，并使用 veraPDF 这个行业参考级开源 PDF/A 和 PDF/UA 验证器进行验证。

### How do I convert existing PDFs to PDF/UA?

OpenDataLoader 提供端到端 pipeline：审核现有 PDF 标签（`use_struct_tree=True`）、将未加标签 PDF 自动加标签为 Tagged PDFs（`format="tagged-pdf"`，Apache 2.0 下免费），并导出为 PDF/UA-1 或 PDF/UA-2（企业附加功能）。Auto-tagging 遵循 PDF Association 的 Well-Tagged PDF specification，并使用 veraPDF 验证。Auto-tagging 生成 Tagged PDF；PDF/UA export 是最后一步。[联系我们](https://opendataloader.org/contact)获取企业集成。

### How do I make my PDFs accessible for EAA compliance?

European Accessibility Act 要求在 2025 年 6 月 28 日前提供可访问的数字产品。OpenDataLoader 支持完整修复工作流：audit → auto-tag → Tagged PDF → PDF/UA export。Auto-tagging 遵循 PDF Association 的 Well-Tagged PDF specification，并使用 veraPDF 验证，确保输出符合标准。Auto-tagging to Tagged PDF 在 Apache 2.0 下开源。PDF/UA export 和 accessibility studio 是企业附加功能。参见我们的 [Accessibility Guide](https://opendataloader.org/docs/accessibility-compliance)。

### Is OpenDataLoader PDF free?

核心库在 **Apache 2.0 下开源**，可免费商用。这包括所有抽取功能（文本、表格、图片、OCR、公式、通过 hybrid mode 处理图表）、AI 安全过滤、Tagged PDF 支持，以及自动加标签到 Tagged PDF。我们承诺保持核心无障碍 pipeline（layout analysis → auto-tagging → Tagged PDF）免费且开源。对于需要端到端法规合规的组织，可使用企业附加功能（PDF/UA export、accessibility studio）。

### Why did the license change from MPL 2.0 to Apache 2.0?

MPL 2.0 要求文件级 copyleft，这通常会在企业采用前触发法律审查。Apache 2.0 是完全宽松许可，没有 copyleft 义务，更容易集成到商业项目中。如果你正在使用 2.0 之前的版本，它仍在 MPL 2.0 下，你可以继续使用。升级到 2.0+ 意味着你的项目遵循 Apache 2.0 条款，该条款严格更宽松，没有额外义务，你这边无需采取任何操作。

## Documentation

- [Quick Start (Python)](https://opendataloader.org/docs/quick-start-python)
- [Quick Start (Node.js)](https://opendataloader.org/docs/quick-start-nodejs)
- [Quick Start (Java)](https://opendataloader.org/docs/quick-start-java)
- [JSON Schema Reference](https://opendataloader.org/docs/reference/json-schema)
- [CLI Options](https://opendataloader.org/docs/reference/cli-options)
- [Hybrid Mode Guide](https://opendataloader.org/docs/hybrid-mode)
- [Tagged PDF Support](https://opendataloader.org/docs/tagged-pdf)
- [AI Safety Features](https://opendataloader.org/docs/ai-safety)
- [PDF Accessibility](https://opendataloader.org/docs/accessibility-compliance)

## Contributing

欢迎贡献！请参阅 [CONTRIBUTING.md](CONTRIBUTING.md) 了解指南。

## License

[Apache License 2.0](LICENSE)

> **注意：** 2.0 之前的版本采用 [Mozilla Public License 2.0](https://www.mozilla.org/MPL/2.0/) 许可。

---

**觉得有用吗？** 给我们一个 star，帮助更多人发现 OpenDataLoader。
