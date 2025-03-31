# SWE-bench 使用笔记
**base_commit**：任务发生时的 Git commit ID
```sh
# 切换到任务发生时的代码版本
git checkout <base_commit_id>  
```
**patch**：gold patch，真实开发者提交的 PR 修复，来源于 GitHub 代码库的历史 PR，用于评估模型 patch 是否接近真实 PR，可能包含代码优化、重构等；  
**test_patch**：最小可行修复，确保 fail-to-pass，来源于研究者从 gold patch 提取最小修改，用于评估 patch 是否有效，即如果应用了 test_patch，FAIL_TO_PASS 就能通过