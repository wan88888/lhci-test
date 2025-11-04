# Lighthouse CI 配置说明

[![Lighthouse CI](https://github.com/YOUR_USERNAME/YOUR_REPO/actions/workflows/lighthouse-ci.yml/badge.svg)](https://github.com/YOUR_USERNAME/YOUR_REPO/actions/workflows/lighthouse-ci.yml)

> 自动化的网站性能监控工具，使用 Lighthouse CI 进行持续性能测试

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
- ✅ 每天定时运行（UTC 00:00）
- ✅ 支持手动触发

查看详细配置：[.github/workflows/README.md](.github/workflows/README.md)

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
本项目已配置完整的 GitHub Actions 工作流！查看配置文件：
- [lighthouse-ci.yml](.github/workflows/lighthouse-ci.yml) - 工作流配置
- [工作流说明文档](.github/workflows/README.md) - 详细使用指南

工作流功能：
- 🔄 自动运行性能测试
- 📊 生成详细的 Lighthouse 报告
- 💬 在 PR 中自动添加测试结果评论
- 📦 上传报告到 GitHub Artifacts（保留 30 天）
- ⏰ 支持定时任务和手动触发

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
如果搭建了自己的 LHCI Server：
```json
"upload": {
  "target": "lhci",
  "serverBaseUrl": "https://your-lhci-server.com",
  "token": "your-build-token"
}
```

## 性能指标阈值参考

| 指标 | 优秀 | 需改进 | 差 |
|------|------|--------|-----|
| FCP | < 1.8s | 1.8s - 3s | > 3s |
| LCP | < 2.5s | 2.5s - 4s | > 4s |
| TBT | < 200ms | 200ms - 600ms | > 600ms |
| CLS | < 0.1 | 0.1 - 0.25 | > 0.25 |
| SI | < 3.4s | 3.4s - 5.8s | > 5.8s |

## 📊 查看测试结果

### 在 GitHub Actions 中查看
1. 进入 GitHub 仓库的 **Actions** 标签
2. 选择 **Lighthouse CI** 工作流
3. 查看最近的运行记录
4. 下载 **lighthouse-reports** Artifact 获取详细报告

### 在 Pull Request 中查看
- 工作流会自动在 PR 中添加评论
- 评论包含性能测试摘要和报告链接

## 📁 项目结构
```
lhci-test/
├── .github/
│   └── workflows/
│       ├── lighthouse-ci.yml      # GitHub Actions 工作流
│       └── README.md              # 工作流说明文档
├── lighthouserc.json              # Lighthouse CI 主配置文件
├── package.json                   # Node.js 项目配置
└── README.md                      # 项目说明文档
```

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

### 如何修改定时任务时间？
编辑 `.github/workflows/lighthouse-ci.yml` 中的 `schedule.cron`：
```yaml
schedule:
  - cron: '0 0 * * *'  # 每天 UTC 00:00
```

## 🌟 更多资源
- [Lighthouse CI 官方文档](https://github.com/GoogleChrome/lighthouse-ci)
- [Lighthouse 评分指南](https://web.dev/performance-scoring/)
- [Web Vitals](https://web.dev/vitals/)
- [GitHub Actions 文档](https://docs.github.com/en/actions)

## 📝 License
MIT
