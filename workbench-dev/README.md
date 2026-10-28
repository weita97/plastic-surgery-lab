# 每日学习工作台 · 开发版

这是**开发版**，与正式版 `../workbench/` 完全隔离，正式版不受任何影响。

| | 正式版 | 开发版（本目录） |
|---|---|---|
| 地址 | `.../plastic-surgery-lab/workbench/` | `.../plastic-surgery-lab/workbench-dev/` |
| 存储前缀 | `wb1_` | `wb1d_` |
| 本地存储键 | `wb1_ledger`、`wb1_en_vocab_batch_cet` … | `wb1d_ledger`、`wb1d_en_vocab_batch_cet` … |
| 数据 | 你日常使用的真实数据 | 空白，可随便试、随便清 |

## 为什么必须分开

GitHub Pages 的所有项目站都挂在 `weita97.github.io` 这**一个域名**下，而浏览器的 localStorage 是按**域名**隔离的，**不看路径**。如果两版用同一个存储前缀，开发版一打开就会读到正式版的数据，甚至把它改掉。

所以开发版构建时把存储前缀换成了 `wb1d_`，两边的 localStorage 键名完全不同，互不干扰。

## 怎么构建和部署

源码只有一份，在 `../workbench/src/`。构建脚本按参数决定输出哪个版本：

```bash
cd workbench

# 构建 + 校验正式版 → ../每日学习工作台.html（前缀 wb1_）
node tools/build.js
node tools/check.js

# 构建 + 校验开发版 → ../每日学习工作台_dev.html（前缀 wb1d_）
node tools/build.js --dev
node tools/check.js --dev

# 一键部署正式版 → workbench/
powershell -ExecutionPolicy Bypass -File tools/deploy.ps1 "提交说明"

# 一键部署开发版 → workbench-dev/（本目录）
powershell -ExecutionPolicy Bypass -File tools/deploy.ps1 "提交说明" -Dev
```

## 开发版自带的保护

打开开发版页面时，顶部会出现一条橙色提示：

> 🧪 开发版 · 数据独立存储（wb1d_），不影响正式版

如果前缀注入失败（说明构建有问题），会变成红条：

> ⚠️ 存储前缀注入失败，请勿使用本页（会污染正式版数据）

`node tools/check.js --dev` 也会校验前缀，注入不对直接报错退出。

## 在开发版里试新功能

1. 改 `workbench/src/` 下的源码（`js/` 或 `data/` 或 `style.css`）
2. `node tools/build.js --dev` → `node tools/check.js --dev`
3. 双击 `每日学习工作台_dev.html` 本地验证，或 `deploy.ps1 ... -Dev` 推到线上验证
4. 满意后再用**不带 `-Dev`** 的 `deploy.ps1` 发布到正式版

正式版 `workbench/` 在开发过程中一次都不会被改动，你照常用。
