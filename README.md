# Lighthouse CI 性能监控项目

[![Lighthouse CI](https://github.com/YOUR_USERNAME/YOUR_REPO/actions/workflows/lighthouse-ci.yml/badge.svg)](https://github.com/YOUR_USERNAME/YOUR_REPO/actions/workflows/lighthouse-ci.yml)

> 自动化的网站性能监控工具，使用 Lighthouse CI 进行持续性能测试，支持 LHCI Server 报告存储和飞书实时通知

## ✨ 特性

- 🚀 **自动化测试** - GitHub Actions 自动运行性能测试
- 📊 **LHCI Server** - 历史报告存储和趋势分析
- 📱 **飞书通知** - 测试完成后自动发送飞书消息
- 🎯 **性能断言** - 自定义性能阈值，不达标自动失败
- 📈 **持续监控** - 支持定时任务和 PR 触发

## 🚀 快速开始

### 本地运行
```bash
# 安装依赖
npm install

# 运行 Lighthouse CI 测试
npm run lhci

# 或分步运行
npm run lhci:collect  # 收集数据
npm run lhci:assert   # 运行断言
npm run lhci:upload   # 上传结果
```

### GitHub Actions 自动化
本项目已配置 GitHub Actions 工作流，会在以下情况自动运行：
- ✅ 推送到 main 分支
- ✅ 创建或更新 Pull Request
- 📱 自动发送飞书通知
- 📊 上传报告到 LHCI Server

查看工作流配置：[.github/workflows/lighthouse-ci.yml](.github/workflows/lighthouse-ci.yml)

## 📋 配置文件：lighthouserc.json

### 主要配置项说明

#### 1. collect（收集配置）
- **url**: 需要测试的 URL 列表
- **numberOfRuns**: 每个 URL 运行的次数（默认 3 次，取中位数）
- **startServerCommand**: 启动本地服务器的命令（可选）
- **startServerReadyPattern**: 服务器就绪的标识字符串
- **settings**: Lighthouse 运行设置
  - **preset**: 预设配置（desktop/mobile）
  - **throttling**: 网络和 CPU 节流配置
  - **screenEmulation**: 屏幕模拟配置

#### 2. assert（断言配置）
- **preset**: 使用推荐的 Lighthouse 预设规则
- **assertions**: 自定义断言规则
  - **categories**: 各项分类的最低分数要求（0-1）
  - **metrics**: 具体性能指标阈值
    - FCP (First Contentful Paint): 首次内容绘制
    - LCP (Largest Contentful Paint): 最大内容绘制
    - CLS (Cumulative Layout Shift): 累积布局偏移
    - TBT (Total Blocking Time): 总阻塞时间
    - SI (Speed Index): 速度指数
    - TTI (Time to Interactive): 可交互时间

#### 3. upload（上传配置）
- **target**: 结果上传目标
  - `temporary-public-storage`: 临时公共存储（7天）
  - `filesystem`: 保存到本地文件系统
  - 自定义 LHCI 服务器 URL

## 使用方法

### 安装
```bash
npm install -g @lhci/cli
# 或项目本地安装
npm install --save-dev @lhci/cli
```

### 运行命令
```bash
# 收集性能数据
lhci collect

# 运行断言检查
lhci assert

# 上传结果
lhci upload

# 一次性运行所有步骤
lhci autorun
```

### GitHub Actions 集成
本项目已配置完整的 GitHub Actions 工作流！

**工作流功能：**
- 🔄 自动运行性能测试
- 📊 上传报告到 LHCI Server
- 📱 发送飞书通知
- 🎯 性能断言检查
- ⚡ 支持 PR 和 push 触发

**查看配置**: [.github/workflows/lighthouse-ci.yml](.github/workflows/lighthouse-ci.yml)

## 配置调整建议

### 移动端配置
如果需要测试移动端性能，修改 settings：
```json
"settings": {
  "preset": "mobile",
  "throttling": {
    "rttMs": 150,
    "throughputKbps": 1638,
    "cpuSlowdownMultiplier": 4
  },
  "screenEmulation": {
    "mobile": true,
    "width": 375,
    "height": 667,
    "deviceScaleFactor": 2,
    "disabled": false
  },
  "formFactor": "mobile"
}
```

### 静态站点配置
如果是静态站点，不需要启动服务器：
```json
"collect": {
  "staticDistDir": "./dist",
  "url": [
    "/index.html",
    "/about.html"
  ],
  "numberOfRuns": 3
}
```

### 使用 LHCI Server
本项目已配置 LHCI Server 上传：
```json
"upload": {
  "target": "lhci"
}
```

通过环境变量配置：
- `LHCI_SERVER_BASE_URL` - LHCI Server 基础地址
- `LHCI_TOKEN` - Build Token

Dashboard URL 会自动拼接为：`${LHCI_SERVER_BASE_URL}/app/projects/lhci-test/dashboard`

## 性能指标阈值参考

| 指标 | 优秀 | 需改进 | 差 |
|------|------|--------|-----|
| FCP | < 1.8s | 1.8s - 3s | > 3s |
| LCP | < 2.5s | 2.5s - 4s | > 4s |
| TBT | < 200ms | 200ms - 600ms | > 600ms |
| CLS | < 0.1 | 0.1 - 0.25 | > 0.25 |
| SI | < 3.4s | 3.4s - 5.8s | > 5.8s |

## 📊 查看测试结果

### 1. LHCI Server（推荐）
访问你的 LHCI Server Dashboard 查看详细报告和历史趋势：
- 查看历史测试记录
- 对比性能变化趋势
- 分析各项指标详情
- Dashboard 地址在 GitHub Secrets 中配置

### 2. 飞书通知
每次测试完成后，会自动发送飞书消息通知：
- ✅ 测试状态（通过/失败）
- 📊 快速查看报告按钮
- 🔗 跳转到 GitHub Actions 详情
- 💻 包含提交信息和作者

### 3. GitHub Actions
1. 进入 GitHub 仓库的 **Actions** 标签
2. 选择 **Lighthouse CI** 工作流
3. 查看最近的运行记录
4. 查看详细日志

## 📁 项目结构
```
lhci-test/
├── .github/
│   └── workflows/
│       └── lighthouse-ci.yml      # GitHub Actions 工作流
├── lighthouserc.json              # Lighthouse CI 主配置文件
├── lighthouserc.examples.json     # 配置示例参考
├── package.json                   # Node.js 项目配置
├── .gitignore                     # Git 忽略文件
└── README.md                      # 项目说明文档
```

## 🔐 环境变量配置

需要在 GitHub Settings → Secrets and variables → Actions 中配置以下 Secrets：

| Secret 名称 | 说明 | 必需 |
|------------|------|------|
| `LHCI_SERVER_BASE_URL` | LHCI Server 地址 | ✅ 是 |
| `LHCI_TOKEN` | LHCI Build Token | ✅ 是 |
| `FEISHU_WEBHOOK` | 飞书机器人 Webhook 地址 | ✅ 是 |

### 配置步骤

#### 1. 配置 LHCI Server
- `LHCI_SERVER_BASE_URL`: 你的 LHCI Server 地址（例如：`https://your-lhci-server.com`）
- `LHCI_TOKEN`: 从 LHCI Server 项目设置中获取

**注意**：Dashboard URL 会自动拼接为 `${LHCI_SERVER_BASE_URL}/app/projects/lhci-test/dashboard`

#### 2. 配置飞书机器人
1. 打开飞书群聊 → 设置 → 群机器人
2. 添加自定义机器人，命名为 "Lighthouse CI"
3. 复制 Webhook 地址
4. 添加到 GitHub Secrets 中

#### 3. GITHUB_TOKEN
- 自动提供，无需手动配置
- 已在工作流中配置权限：`contents: read`, `pull-requests: write`, `statuses: write`

## 🔧 常见问题

### 如何修改测试的 URL？
编辑 `lighthouserc.json` 中的 `collect.url` 数组：
```json
{
  "collect": {
    "url": [
      "https://your-website.com",
      "https://your-website.com/about"
    ]
  }
}
```

### 如何调整性能阈值？
编辑 `lighthouserc.json` 中的 `assert.assertions`：
```json
{
  "assert": {
    "assertions": {
      "categories:performance": ["error", {"minScore": 0.9}]
    }
  }
}
```

### 如何添加定时任务？
在 `.github/workflows/lighthouse-ci.yml` 的 `on:` 部分添加：
```yaml
on:
  push:
    branches:
      - main
  pull_request:
  schedule:
    - cron: '0 16 * * 3'  # 每周三 UTC 16:00 (北京时间周四 00:00)
  workflow_dispatch:      # 支持手动触发
```

### 如何查看飞书通知？
每次测试完成后，飞书会收到包含以下信息的消息卡片：
- 测试状态（通过/失败，绿色/红色卡片）
- 分支名称和提交信息
- 两个按钮：查看详细报告、查看 Actions

### 测试 URL 在哪里配置？
当前配置的测试 URL: `https://esimnum.com/home`

修改 `lighthouserc.json` 中的 `collect.url`：
```json
{
  "collect": {
    "url": ["https://your-website.com"]
  }
}
```

## 🌟 更多资源
- [Lighthouse CI 官方文档](https://github.com/GoogleChrome/lighthouse-ci)
- [Lighthouse 评分指南](https://web.dev/performance-scoring/)
- [Web Vitals](https://web.dev/vitals/)
- [GitHub Actions 文档](https://docs.github.com/en/actions)

## 📝 License
MIT
