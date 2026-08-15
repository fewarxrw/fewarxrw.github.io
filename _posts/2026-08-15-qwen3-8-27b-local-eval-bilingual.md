---
title: "Qwen3.8-27B 本地能力实测报告 / Local Evaluation Report of Qwen3.8-27B"
date: 2026-08-15
description: "基于 96GB 单卡本地环境的单机小样本实测：编程、中文公文、Excel 表格与推理性能。"
categories: [notes]
tags: [llm, qwen, benchmark, local-deployment, evaluation]
---

> 本文为原报告的扩充版，采用中英对照排版；
> 图片与截图已省略，只保留可复核的文本与数值。

## 一、总结 / Executive Summary

本次测评在单机单 GPU 下，对 Qwen/Qwen3.8-27B 进行了结构化实测：编程、中文公文和 Excel 表格自动化任务，以及本地推理性能。综合判断是：该模型在结构化中文文本输出与代码修复上表现稳定，但不应被误读为万能模型或 GUI 自动化专家。

The model was evaluated on a single-machine, single-GPU setup for code tasks, Chinese formal-document tasks, Excel-automation workflows, and local inference performance. The practical conclusion is that Qwen3.8-27B is solid on structured code and office tasks, but it should not be interpreted as universally superior or a fully reliable GUI automation solution.

### 核心成绩 / Key Scores

| 项目 / Dimension | 得分 / Score | 通过率 / Pass Rate | 结论 / Takeaway |
|---|---:|---:|---|
| 编程 / Coding tasks | 25 / 26 | 96.2% | 结构化修复能力强；1 个边界输入失败 | 
| 中文公文 / Official text tasks | 76 / 76 | 100% | 规则约束下稳定输出 |
| Excel 自动化 / Excel automation | 14 / 14 | 100% | 可生成可复用结果文件 |
| 推理性能 / Inference performance | 26.451 tok/s（single_stream） | -- | 小并发可用；资源开销高 |

关键结论：
- 可用于结构化研发与办公场景（修复类代码任务、标准化公文、报表生成）。
- 异常输入和长输出边界仍需规则防护与人工复核。
- 该测评是小样本、单环境结果，不可直接替代公开基准。

The score profile is stable enough for internal workflows that have clear constraints, but it needs guardrails for edge cases and output control. This is a small-sample, single-environment benchmark and should not be treated as a general benchmark claim.

## 二、模型与环境 / Model and Environment

**模型**
- 名称 / Model: `Qwen/Qwen3.8-27B`
- Revision: `1d4bf0f2ff6012fd82039f2fa52739d0dd7c60c0`
- 权重 / Weights: 官方 BF16 Transformers 权重（Apache-2.0）
- 上下文长度 / Context length: 官方 262,144，本次限制 65,536
- 推理后端 / Inference stack: vLLM 0.27.1

**本地环境 / Local stack**
- GPU: NVIDIA RTX PRO 6000 Blackwell Workstation Edition（96GB）
- 驱动 / Driver: 580.126.09，CUDA 13.0
- 主机 / Host: Linux-6.17.0-35-generic（glibc2.39）
- Python: 3.12.3
- CPU / 内存 / Memory: 60 核 / 120 线程，系统内存 250.92 GiB
- 模型文件 / Model size: 51.747 GiB（18 个分片）

This setup was intentionally single-machine and hardware-faithful: a realistic local deployment footprint rather than an over-optimized cloud benchmark.

## 三、方法与评测流程 / Methodology

### 任务范围 / Scope

- **编程**：2 个工具代理任务（仓库修复型）
- **中文公文**：3 个约束生成任务（formal Chinese generation）
- **Excel**：1 个 openpyxl 工作簿生成与校验任务
- **性能**：4 类单请求测试 + 4 并发压力测试

### 评分原则 / Scoring principle

采用“失败原样计分”方式，任务失败不改写回答；统一基于可复核摘要。中文公文与 Excel 为规则化验证；编程为测试集通过率；性能以可追踪的吞吐与资源采样指标汇总。

### 可追溯性 / Reproducibility

关键指标来自本地任务日志与验收 summary，采用固定随机与可复现配置。报告中结论默认绑定当前栈版本、采样参数与硬件环境。

## 四、结果与解读（含扩展） / Results and Interpretation

### 1. 编程 / Coding

- 任务一：`pricing_engine`
  - 公开测试：5/5
  - 隐藏测试：10/10
  - 小计：15/15

- 任务二：`log_analyzer`
  - 公开测试：3/3
  - 隐藏测试：10/11
  - 小计：10/11
  - 失败案例：`test_start_after_end` 未在 `start > end` 时抛出 `ValueError`（边界校验遗漏）

综合为 25/26（96.2%）。该失败属于语义边界规则缺失，而非执行层崩溃。

This indicates reliable multi-step tool usage for common bugfix workflows, with a concrete weakness in exception-handling guards for malformed input ranges. In production, add negative-case tests or schema-based validation before allowing model-produced changes.

### 2. 中文公文 / Chinese official writing

三项任务均全量通过：
- `training_notice`: 27/27
- `incident_report`: 24/24
- `archive_request`: 25/25

合计：76/76（100%）。

规则体系为：
- `required`：必要信息是否齐全
- `forbidden`：不允许出现不当/越权措辞
- `format`：结构与篇式是否合规

本评测在规则层允许中文正式场景中的等价表述（例如正式时间写法、审批/采购措辞），未改动原始模型输出文本。

The document outputs were highly consistent with enterprise writing constraints under deterministic checks. This is strongest evidence that the model performs well when output constraints are clear and machine-checkable.

### 3. Excel 自动化 / Excel automation

任务 `sales_workbook` 重算后 14/14（100%）。

检查覆盖：
- 明细保留与完整性
- 区域汇总覆盖（含四个区域）
- 销售额、毛利公式一致性
- 明细冻结、筛选、跨表公式引用
- 图表、样式与条件格式

该结果说明模型可将自然语言需求落到 openpyxl 脚本层，并输出可打开可复用的 xlsx 文件。

This is a practical signal for internal office automation (lightweight reporting). It is not a proof of correctness on complex multi-sheet enterprise templates.

### 4. 推理性能 / Inference Performance

- `warmup_non_thinking`：TTFT 约 0.353 s
- `single_stream_512`：TTFT 约 0.101 s，解码 26.451 tok/s
- `reasoning_medium`：25.228 tok/s
- `multimodal_ui_read`：25.636 tok/s
- 4 并发（1024 tokens）：10.576 s，聚合 96.824 tok/s

资源上限（采样峰值）：
- 显存 / Peak memory：90253 MiB
- GPU 利用率：100%
- 功耗 / Power：564.68 W
- 温度 / Temperature：87.0 °C

结论：性能可支撑中等交互负载；当上下文/并发继续扩展时应关注显存与热管理约束。

Performance is suitable for local interactive workloads and bounded throughput use cases, but sustained high-load operation should incorporate queueing, rate limits, and thermal/power monitoring.

## 五、风险边界 / Limitations and Risk Controls

1. 样本规模较小：2 个编程任务、3 个公文任务、1 个 Excel 任务，不可外推到所有真实业务场景。
2. 任务边界清晰但非“开放问题”：更接近约束下的可验证场景。
3. 长输出控制仍有边界风险：强制满长输出场景出现聊天控制 token 泄漏风险，需设置停止词与后处理。
4. 异常输入风险：时间顺序、异常参数和边界条件仍需额外规则层保护。

For production use, the safer deployment pattern is: deterministic schema validation + human review on critical paths + explicit output guardrails.

## 六、发布建议（适用于你个人主页） / Deployment Recommendation

如果你要在个人主页发布并用于分享：

- 保持这篇为“可复核但非官方 benchmark”的标签。
- 在标题中明确“本地单样本实测”与“非公开基准对比”。
- 在正文末尾附上可复核日志路径（或说明仅内部可访问）。
- 强调“图片在此版本省略，仅保留关键文本指标”。

If you publish this article, keep the context explicit: *single-machine, local, and limited-sample*. This avoids over-claiming and makes the report credible.

## 七、参考 / References

- `deliverables/report_content_qwen.json`
- `deliverables/Qwen3.8-27B本地能力与性能测评报告.docx`
- `evaluation/results/code/summary.json`
- `evaluation/results/documents/summary.json`
- `evaluation/results/performance/summary.json`
