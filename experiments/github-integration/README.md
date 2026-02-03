# GitHub API 集成测试

## 实验目标

验证 OpenClaw Bot 的 GitHub 自动化能力，包括仓库管理、代码提交、API 调用等功能。

## 测试环境

- **测试时间:** 2026-02-03 03:46 - 04:04 UTC
- **GitHub 账户:** oyzh888 (ID: 16219422)
- **认证方式:** Personal Access Token
- **工具:** Git CLI, GitHub REST API

## 测试流程

### 1. Token 配置

```bash
# 配置环境变量
export GITHUB_TOKEN="github_pat_***"

# 持久化配置
echo 'export GITHUB_TOKEN="..."' >> ~/.bashrc
echo 'export GITHUB_TOKEN="..."' >> ~/.zshrc

# Git credential store
git config --global credential.helper store
```

**结果:** ✅ 配置成功

### 2. 身份验证

```bash
curl -H "Authorization: Bearer $GITHUB_TOKEN" \
     https://api.github.com/user
```

**返回信息:**
```json
{
  "login": "oyzh888",
  "id": 16219422,
  "type": "User",
  ...
}
```

**结果:** ✅ 认证成功

### 3. 仓库创建

**API 调用:**
```bash
curl -H "Authorization: Bearer $GITHUB_TOKEN" \
     -d '{"name":"openclaw-test","description":"...","private":false}' \
     https://api.github.com/user/repos
```

**创建的仓库:**
- `oyzh888/openclaw-test` (公开)

**结果:** ✅ 创建成功

### 4. 代码推送

**操作步骤:**
```bash
# 初始化仓库
git init
git add .
git commit -m "Initial commit: Test from OpenClaw bot"

# 配置远程
git remote add origin https://$TOKEN@github.com/oyzh888/openclaw-test.git

# 推送代码
git push -u origin main
```

**推送内容:**
- README.md (192 bytes)
- test.py (103 bytes)

**结果:** ✅ 推送成功

### 5. API 验证

```bash
curl -H "Authorization: Bearer $GITHUB_TOKEN" \
     https://api.github.com/repos/oyzh888/openclaw-test/contents
```

**验证结果:**
```json
[
  {"name": "README.md", "size": 192},
  {"name": "test.py", "size": 103}
]
```

**结果:** ✅ 验证通过

## 权限测试

### ✅ 成功的操作
- [x] 读取用户信息
- [x] 创建个人仓库
- [x] 推送代码到个人仓库
- [x] 删除个人仓库
- [x] 读取仓库内容

### ❌ 失败的操作（权限不足）
- [ ] 创建组织仓库 (when2buy org)
- [ ] 推送到组织仓库

**错误信息:**
```
"You need admin access to the organization before adding a repository to it."
```

## 测试结论

### 成功项
1. ✅ **GitHub Token 认证** - 正常工作
2. ✅ **个人仓库管理** - 创建、删除、推送均正常
3. ✅ **Git 操作** - Commit, Push 流程完整
4. ✅ **API 调用** - REST API 访问正常

### 限制项
1. ⚠️ **组织权限** - 当前 token 无法操作组织仓库
2. ⚠️ **高级功能** - 需要更多权限（admin:org, workflow 等）

## 技术细节

### API 认证方式

**方式1: Bearer Token (推荐)**
```bash
-H "Authorization: Bearer $GITHUB_TOKEN"
```

**方式2: URL 嵌入 (不推荐，但适用于 Git)**
```bash
https://$TOKEN@github.com/user/repo.git
```

### Token 权限需求

**当前已有权限:**
- `repo` - 仓库完整访问（个人）

**建议添加权限:**
- `admin:org` - 组织管理
- `workflow` - GitHub Actions
- `delete_repo` - 删除仓库

## 下一步计划

- [ ] 获取组织管理权限
- [ ] 实现自动化 PR 创建
- [ ] 集成 GitHub Actions
- [ ] 设置 Webhooks
- [ ] Issue 自动化管理

## 参考资源

- [GitHub REST API 文档](https://docs.github.com/rest)
- [Personal Access Token 指南](https://docs.github.com/authentication)
- [Git Credential 配置](https://git-scm.com/docs/git-credential-store)

---

**测试完成时间:** 2026-02-03 04:04 UTC  
**测试状态:** ✅ 通过
