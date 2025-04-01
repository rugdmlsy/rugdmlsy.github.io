---
layout: post
title: "SWE-bench 使用笔记"
date:   2025-03-31 15:50:00 +0800
categories: tech
tags: [llm, ai]
---
## 1. SWE-bench 介绍
SWE-bench 是由 Carlos E. Jimenez 等人提出的一个专门用于评估 LLM 解决真实软件工程问题能力的 benchmark，其收集了来自 Github 上 12 个流行的开源 Python 项目中 2294 个已解决的 Issue-Pull Request 对，每个任务要求模型根据 Issue 描述修改代码库，生成补丁并通过单元测试验证。

他们建立了一个[官方网站](https://www.swebench.com/)，可以通过官网很方便地阅读其[论文](https://arxiv.org/abs/2310.06770)、查看 Leaderboard、访问项目的 [Github 主页](https://github.com/swe-bench/SWE-bench)、获取[数据集](https://huggingface.co/datasets/princeton-nlp/SWE-bench)。

## 2. 数据集介绍
以 [SWE-bench_Lite_oracle](https://huggingface.co/datasets/princeton-nlp/SWE-bench_Lite_oracle) 为例：

- **instance_id**：实例 ID

- **text**：SWE-bench 团队给出的提交给 LLM 的 Prompt，其中包括：
    - 原始 issue 内容
    - 项目的 Readme 文件
    - oracle 代码（需要修改的代码文件）
    - 需要生成的 patch 样例模版

- **repo**：仓库名

- **base_commit**：任务发生时的 Git commit ID
    ```sh
    # 切换到任务发生时的代码版本
    git checkout <base_commit_id>  
    ```

- **hints_text**：提示文本，issue 中的一些讨论，*可能包含误导信息*

- **created_at**：创建时间

- **patch**：gold patch，真实开发者提交的 PR 修复，来源于 GitHub 代码库的历史 PR，用于评估模型 patch 是否接近真实 PR，可能包含代码优化、重构等

- **test_patch**：最小可行修复，确保 fail-to-pass，来源于研究者从 gold patch 提取最小修改，用于评估 patch 是否有效，即如果应用了 test_patch，FAIL_TO_PASS 就能通过

- **version**：版本号

- **FAIL_TO_PASS**：复现测试用例，原始代码应不能通过

- **PASS_TO_PASS**：回归测试用例，任何时候都应通过

- **environment_setup_commit**：由于项目开发过程中 requirements 文件可能没有及时更新导致依赖不匹配，可通过该版本配置环境以进行测试

## 3. 使用数据集

安装 datasets 库：
```sh
pip install datasets
```
导入数据集：
```py
from datasets import load_dataset
# 根据需要选择 dev 或 test
ds = load_dataset("princeton-nlp/SWE-bench_Lite_oracle", split="test") 
```
使用数据集：
```py
print(ds[0]['instance_id'])
```

## 4. 测试生成的补丁
使用 SWE-bench 官方的测试程序，需要先将补丁组织成 predictions.json 文件：
```json
[
    {
        "instance_id": "<Unique task instance ID>",
        "model_patch": "<.patch file content string>",
        "model_name_or_path": "<Model name here (i.e. SWE-Llama-13b)>",
    },
    {
        ...
    }
]
```
或者
```json
{
    "instance_id_1": {
        "model_patch": "...",
        "model_name_or_path": "..."
    },
    "instance_id_2": {
        "model_patch": "...",
        "model_name_or_path": "..."
    }
}
```
然后可以使用本地测试或在线测试。由于本地测试需要的资源较多（官方文档中提到至少需要 120GB 的存储空间，16 GB 的内存和 8 核处理器），故推荐使用 [sb-cli](https://github.com/swe-bench/sb-cli) 进行在线测试。

### 配置 sb-cli
安装 sb-cli：
```sh
pip install sb-cli
```

生成 API key：
```sh
sb-cli gen-api-key your.email@example.com
```

设置环境变量：
```sh
export SWEBENCH_API_KEY=your_api_key
# or add export SWEBENCH_API_KEY=your_api_key to your .*rc file
```

收到验证邮件后进行验证：
```sh
sb-cli verify-api-key YOUR_VERIFICATION_CODE
```
### 使用 sb-cli
SWE-bench 有 3 个 subsets，每个 subset 有两个 splits，使用时需要指定 subset 和 split。

**Subsets**
- swe-bench-m: The main dataset
- swe-bench_lite: A smaller subset for testing and development
- swe-bench_verified: 500 verified problems from SWE-bench

**Splits**
- dev: Development/validation split
- test: Test split (currently only available for swe-bench_lite)

常用命令：
```sh
# 提交 predictions 用于验证
sb-cli submit swe-bench-m dev --predictions_path preds.json --run_id my_run

# 获取验证报告
sb-cli get-report swe-bench-m dev my_run

# 列出运行记录
sb-cli list-runs swe-bench-m dev

# 删除运行记录
sb-cli delete-run swe-bench-m dev old_run_id
```
如果想要将测试结果上传至排行榜，可以参考[官网](https://www.swebench.com/submit.html)和 [sb-cli 文档](https://www.swebench.com/sb-cli/submit-to-leaderboard/).