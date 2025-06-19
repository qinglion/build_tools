# GitHub Workflow 使用指南

## Docker 构建与发布 Workflow

本项目已配置了自动化的 GitHub workflow，可以使用 Docker 构建 ONLYOFFICE 产品并自动发布到 GitHub Releases。

### Workflow 文件

- **文件位置**: `.github/workflows/docker-build-release.yml`
- **功能**: 使用 Docker 构建项目并将产物打包为 zip 文件发布

### 触发条件

#### 1. 自动触发（推荐）
当推送版本标签时自动触发：

```bash
# 创建并推送版本标签
git tag v1.0.0
git push origin v1.0.0
```

#### 2. 手动触发
在 GitHub 仓库页面：
1. 进入 "Actions" 标签页
2. 选择 "Docker Build and Release" workflow
3. 点击 "Run workflow" 按钮
4. 选择分支并点击 "Run workflow"

### 构建流程

1. **环境准备**: 检出代码，设置 Docker Buildx
2. **Docker 构建**: 
   ```bash
   docker build --tag onlyoffice-document-editors-builder .
   ```
3. **产品构建**: 
   ```bash
   docker run -v $PWD/out:/build_tools/out onlyoffice-document-editors-builder
   ```
4. **打包**: 将 `./out` 目录打包为 zip 文件
5. **发布**: 
   - 如果是标签触发：创建 GitHub Release 并上传 zip 文件
   - 如果是手动触发：上传为 GitHub Artifacts

### 输出产物

#### GitHub Release（标签触发）
- **文件名**: `onlyoffice-build-{版本号}.zip`
- **内容**: 编译后的 ONLYOFFICE 产品
- **下载**: 从仓库的 Releases 页面下载

#### GitHub Artifacts（手动触发）
- **文件名**: `onlyoffice-build-{时间戳}`
- **内容**: 编译后的 ONLYOFFICE 产品
- **下载**: 从 workflow 运行页面下载
- **保留期**: 30 天

### 版本管理

推荐使用语义化版本号：
- `v1.0.0` - 主要版本
- `v1.1.0` - 次要版本  
- `v1.1.1` - 补丁版本

### 使用发布的产物

1. 从 Releases 页面下载对应版本的 zip 文件
2. 解压到目标目录
3. 按照主 README.md 中的说明配置和运行

### 故障排除

#### 常见问题

1. **Docker 构建失败**
   - 检查 Dockerfile 是否正确
   - 确认依赖包可以正常安装

2. **构建超时**
   - GitHub Actions 有时间限制（通常 6 小时）
   - 考虑优化构建过程或使用自托管 runner

3. **Release 创建失败**
   - 检查标签名称是否符合 `v*` 模式
   - 确认有足够的仓库权限

#### 查看构建日志

1. 进入仓库的 "Actions" 页面
2. 选择对应的 workflow 运行
3. 点击失败的步骤查看详细日志

### 自定义配置

如需修改构建行为，可以编辑 `.github/workflows/docker-build-release.yml` 文件：

- 修改触发条件
- 调整 Docker 构建参数
- 自定义打包逻辑
- 更改发布描述模板 