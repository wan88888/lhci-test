# Lighthouse CI 配置说明

## 配置文件：lighthouserc.json

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

### CI/CD 集成示例（GitHub Actions）
```yaml
name: Lighthouse CI
on: [push]
jobs:
  lighthouse:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: 18
      - run: npm install
      - run: npm install -g @lhci/cli
      - run: npm run build
      - run: lhci autorun
```

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

## 更多资源
- [Lighthouse CI 官方文档](https://github.com/GoogleChrome/lighthouse-ci)
- [Lighthouse 评分指南](https://web.dev/performance-scoring/)
- [Web Vitals](https://web.dev/vitals/)

