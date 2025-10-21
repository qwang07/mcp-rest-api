# 文件上传功能测试指南

## 🎯 测试步骤

### 准备工作

1. **安装测试服务器依赖**（已完成）：
```bash
pnpm add -D express multer
```

2. **测试文件已准备好**：
   - `test/test-file.txt`
   - `test/test-file-2.txt`
   - `test/uploads/` 目录已创建

3. **启动测试服务器**：
```bash
node test/test-server.js
```

---

## 📝 测试场景

### 场景1️⃣: 测试单文件上传

**配置MCP服务器**（设置环境变量）：
```bash
REST_BASE_URL=http://localhost:3000
```

**使用MCP Inspector测试**：
```bash
pnpm run inspector
```

在Inspector中调用 `test_request` 工具：
```json
{
  "method": "POST",
  "endpoint": "/upload/single",
  "files": [
    {
      "fieldName": "avatar",
      "filePath": "./test/test-file.txt"
    }
  ]
}
```

**预期结果**：
- ✅ 服务器控制台显示收到文件
- ✅ Inspector显示200状态码
- ✅ 响应包含文件信息

---

### 场景2️⃣: 测试多文件上传

```json
{
  "method": "POST",
  "endpoint": "/upload/multiple",
  "files": [
    {
      "fieldName": "files",
      "filePath": "./test/test-file.txt"
    },
    {
      "fieldName": "files",
      "filePath": "./test/test-file-2.txt"
    }
  ]
}
```

---

### 场景3️⃣: 测试文件+表单字段

```json
{
  "method": "POST",
  "endpoint": "/upload/single",
  "files": [
    {
      "fieldName": "avatar",
      "filePath": "./test/test-file.txt",
      "fileName": "custom-name.txt",
      "contentType": "text/plain"
    }
  ],
  "formFields": {
    "title": "我的文件",
    "description": "测试上传"
  }
}
```

---

### 场景4️⃣: 测试文件大小限制

创建一个大文件：
```bash
# 创建15MB的文件（超过默认10MB限制）
dd if=/dev/zero of=test/large-file.bin bs=1M count=15
```

测试：
```json
{
  "method": "POST",
  "endpoint": "/upload/single",
  "files": [
    {
      "fieldName": "avatar",
      "filePath": "./test/large-file.bin"
    }
  ]
}
```

**预期结果**：
- ❌ 应该抛出错误："文件超过大小限制"

---

### 场景5️⃣: 测试路径遍历攻击防护

```json
{
  "method": "POST",
  "endpoint": "/upload/single",
  "files": [
    {
      "fieldName": "avatar",
      "filePath": "../../../etc/passwd"
    }
  ]
}
```

**预期结果**：
- ❌ 应该抛出错误："路径遍历攻击已被检测到"

---

### 场景6️⃣: 测试不存在的文件

```json
{
  "method": "POST",
  "endpoint": "/upload/single",
  "files": [
    {
      "fieldName": "avatar",
      "filePath": "./test/non-existent-file.txt"
    }
  ]
}
```

**预期结果**：
- ❌ 应该抛出错误："文件不存在或无法读取"

---

## 🔧 使用curl直接测试（不依赖MCP）

如果你想直接测试服务器而不通过MCP：

```bash
# 测试健康检查
curl http://localhost:3000/health

# 测试文件上传
curl -X POST http://localhost:3000/upload/single \
  -F "avatar=@test/test-file.txt" \
  -F "title=My File"
```

---

## 🐛 调试技巧

1. **查看详细日志**：
   - 测试服务器会在控制台打印收到的所有数据
   - MCP服务器的错误会显示在Inspector中

2. **检查上传的文件**：
   ```bash
   ls -lh test/uploads/
   ```

3. **查看文件内容**：
   ```bash
   cat test/uploads/文件名
   ```

---

## ✅ 验收标准

所有以下场景都应该正常工作：

- [ ] 单文件上传成功
- [ ] 多文件上传成功
- [ ] 文件+表单字段混合上传成功
- [ ] 自定义文件名和contentType生效
- [ ] 文件大小限制正常工作
- [ ] 路径遍历攻击被拦截
- [ ] 不存在的文件被正确处理
- [ ] 响应数据格式正确
- [ ] 服务器能正确接收和保存文件

---

## 📊 测试报告模板

```markdown
## 测试结果

### 环境信息
- Node版本: v24.10.0
- pnpm版本: 10.18.3
- 测试服务器: http://localhost:3000

### 测试场景
| 场景 | 状态 | 备注 |
|------|------|------|
| 单文件上传 | ⬜ | - |
| 多文件上传 | ⬜ | - |
| 文件+表单 | ⬜ | - |
| 大小限制 | ⬜ | - |
| 路径安全 | ⬜ | - |
| 错误处理 | ⬜ | - |

### 发现的问题
-

### 总结

```
