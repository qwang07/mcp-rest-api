# 测试目录说明

这个目录包含所有测试相关的文件和工具。

## 📁 目录结构

```
test/
├── README.md                    # 本文件
├── test-server.js              # 文件上传测试服务器
├── test-upload.md              # 详细测试指南
├── test-file.txt               # 测试用文件1
├── test-file-2.txt             # 测试用文件2
├── uploads/                    # 测试服务器上传文件保存目录（gitignore）
├── fixtures/                   # 单元测试用的fixture文件
│   ├── error-handling/         # 错误处理测试fixtures
│   ├── file-size/              # 文件大小测试fixtures
│   └── form-data/              # FormData测试fixtures
├── *.test.js                   # 单元测试文件
│   ├── file-validation.test.js
│   ├── file-size.test.js
│   ├── form-data.test.js
│   └── error-handling.test.js
```

## 🧪 运行测试

### 单元测试
```bash
# 运行所有单元测试
pnpm test

# 监视模式（文件变化时自动重新运行）
pnpm test:watch

# 带覆盖率报告
pnpm test:coverage
```

### 集成测试（需要测试服务器）

**步骤1：启动测试服务器**
```bash
pnpm test:server
# 或
node test/test-server.js
```

**步骤2：使用MCP Inspector测试**
```bash
pnpm run inspector
```

详细测试步骤请查看 `test-upload.md`。

## 📝 测试文件说明

### 单元测试文件
- **file-validation.test.js**: 测试文件路径验证（路径遍历攻击防护）
- **file-size.test.js**: 测试文件大小检查和文件存在性验证
- **form-data.test.js**: 测试FormData构建（单/多文件、表单字段、body）
- **error-handling.test.js**: 测试各种错误场景

### 测试工具
- **test-server.js**: Express服务器，用于接收和验证文件上传
- **test-upload.md**: 详细的手动测试指南

### 测试数据
- **test-file.txt**: 小文本文件（用于基本上传测试）
- **test-file-2.txt**: 第二个测试文件（用于多文件上传）
- **uploads/**: 测试服务器接收的文件保存位置（自动创建）

## 🎯 测试覆盖范围

当前测试覆盖：
- ✅ 文件路径验证（6个测试）
- ✅ 文件大小检查（4个测试）
- ✅ FormData构建（7个测试）
- ✅ 错误处理（6个测试）

总计：**23个单元测试**

## 📋 添加新测试

1. 在 `test/` 目录创建 `*.test.js` 文件
2. 使用Node.js原生test runner语法：
```javascript
import { test } from 'node:test';
import assert from 'node:assert';

test('测试描述', () => {
  // 测试代码
  assert.strictEqual(1 + 1, 2);
});
```

3. 运行 `pnpm test` 查看结果

## 🧹 清理测试文件

```bash
# 清理测试服务器上传的文件
rm -rf test/uploads/*

# 清理fixtures（会自动重建）
rm -rf test/fixtures/*

# 重新生成测试fixtures
pnpm test
```
