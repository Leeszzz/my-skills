# index.json Schema 参考

索引文件的位置由输出格式决定：
- `CLAUDE.md` → `.claude/index.json`
- `AGENTS.md` → `.agents/index.json`

索引文件记录扫描元数据，支持增量更新和断点续扫。

## Schema 版本

当前版本：`2.0`

## 完整 Schema

```json
{
  "version": "2.0",
  "timestamp": "2026-05-07T12:00:00+08:00",
  "last_scan_timestamp": "2026-05-06T10:30:00+08:00",
  "output_format": "CLAUDE.md",
  "scan_config": {
    "strategy": "standard",
    "parallel_enabled": true,
    "max_parallel_modules": 5,
    "timeout_per_module_ms": 60000
  },
  "changes_summary": {
    "new_modules": ["packages/new-module"],
    "removed_modules": [],
    "updated_modules": ["packages/auth", "services/api"],
    "new_files_count": 15,
    "removed_files_count": 3,
    "unchanged_files_count": 402
  },
  "project": {
    "name": "项目名称",
    "root_path": ".",
    "description": "项目简短描述",
    "primary_language": "TypeScript",
    "frameworks": ["Next.js", "NestJS"],
    "total_files_estimated": 500,
    "total_files_scanned": 420,
    "total_lines_scanned": 45000,
    "coverage_percent": 84.0,
    "truncated": false,
    "scan_duration_ms": 12500,
    "scan_start_time": "2026-05-07T12:00:00+08:00",
    "scan_end_time": "2026-05-07T12:00:12+08:00"
  },
  "quality_scores": {
    "overall": 78,
    "documentation_completeness": 82,
    "test_coverage": 75,
    "api_documentation": 80,
    "configuration_documentation": 70
  },
  "modules": [
    {
      "name": "模块名称",
      "path": "packages/module-name",
      "language": "TypeScript",
      "framework": "Express",
      "status": "stable",
      "entry_files": ["src/index.ts"],
      "interfaces": ["src/routes/*.ts"],
      "cli_commands": [],
      "exported_functions": ["src/utils.ts"],
      "events": [],
      "hooks": [],
      "protocol_definitions": [],
      "tests": {
        "exists": true,
        "path": "tests/",
        "framework": "Jest",
        "test_files_count": 12,
        "estimated_coverage_percent": 85.0
      },
      "config_files": ["tsconfig.json", "jest.config.ts"],
      "config_format": "JSON",
      "environment_variables": ["PORT", "DATABASE_URL"],
      "data_models": ["prisma/schema.prisma"],
      "database_type": "PostgreSQL",
      "orm": "Prisma",
      "doc_path": "packages/module-name/{{DOC_FILENAME}}",
      "quality_score": 85,
      "coverage": {
        "scanned": 45,
        "estimated": 50,
        "percent": 90.0,
        "gaps": []
      },
      "dependencies": {
        "runtime": ["express", "prisma"],
        "dev": ["jest", "typescript"],
        "internal": ["packages/shared-utils"]
      },
      "security": {
        "sensitive_patterns_found": 0,
        "auth_required": true,
        "auth_type": "JWT"
      }
    }
  ],
  "frameworks_detected": [
    {
      "name": "Next.js",
      "version": "14.0.0",
      "type": "frontend",
      "config_files": ["next.config.js"],
      "modules_using": ["packages/web"]
    },
    {
      "name": "NestJS",
      "version": "10.0.0",
      "type": "backend",
      "config_files": ["nest-cli.json"],
      "modules_using": ["services/api"]
    }
  ],
  "ignore_stats": {
    "rules_source": ".gitignore",
    "ignored_patterns": ["node_modules/**", ".git/**", "dist/**"],
    "ignored_file_count": 80,
    "ignored_by_type": {
      "node_modules": 45,
      "build_output": 20,
      "version_control": 10,
      "binary_files": 5
    }
  },
  "security_scan": {
    "enabled": true,
    "findings": [
      {
        "type": "api_key",
        "severity": "high",
        "file": "config/production.js",
        "line": 15,
        "description": "Potential API key hardcoded"
      }
    ],
    "total_findings": 1,
    "high_severity_count": 1,
    "medium_severity_count": 0,
    "low_severity_count": 0
  },
  "performance_metrics": {
    "scan_duration_ms": 12500,
    "modules_per_second": 0.4,
    "files_per_second": 33.6,
    "memory_peak_mb": 128,
    "tool_calls_count": 156,
    "tool_calls_failed": 2,
    "retry_count": 3
  },
  "failed_modules": [
    {
      "path": "packages/problematic",
      "error": "Permission denied",
      "error_type": "file_access",
      "retry_count": 3,
      "last_attempt": "2026-05-07T12:00:10+08:00"
    }
  ],
  "gaps": [
    {
      "module": "packages/auth",
      "type": "missing_tests",
      "description": "未找到测试目录或测试文件",
      "priority": "high",
      "suggested_path": "packages/auth/tests/",
      "effort_estimate": "2-4 hours"
    },
    {
      "module": "services/api",
      "type": "missing_api_docs",
      "description": "API 路由缺少详细描述",
      "priority": "medium",
      "suggested_action": "补充 JSDoc/Swagger 注释"
    }
  ],
  "next_scan_hints": [
    "packages/auth/src/controllers — 建议优先补扫",
    "services/audit/migrations — 数据模型不完整"
  ],
  "file_hashes": {
    "packages/auth/src/index.ts": "abc123def456",
    "packages/auth/package.json": "789ghi012jkl",
    "services/api/src/main.ts": "mno345pqr678"
  }
}
```

## 字段说明

### 顶层字段

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `version` | string | ✅ | Schema 版本 |
| `timestamp` | string | ✅ | 本次扫描时间戳（ISO-8601 格式） |
| `last_scan_timestamp` | string | ❌ | 上次扫描时间戳，首次运行时不存在 |
| `output_format` | string | ✅ | 输出格式：`CLAUDE.md` 或 `AGENTS.md` |
| `changes_summary` | object | ❌ | 本次扫描相对于上次的变化摘要，首次运行时不存在 |
| `project` | object | ✅ | 项目级元数据 |
| `modules` | array | ✅ | 模块列表 |
| `ignore_stats` | object | ✅ | 忽略规则统计 |
| `gaps` | array | ✅ | 缺口清单 |
| `next_scan_hints` | array | ✅ | 下次扫描建议 |

### changes_summary 字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `new_modules` | string[] | 本次新增的模块路径 |
| `removed_modules` | string[] | 本次移除的模块路径 |
| `updated_modules` | string[] | 本次内容有变化的模块路径 |
| `new_files_count` | number | 本次新增的文件数量 |
| `removed_files_count` | number | 本次移除的文件数量 |

### project 字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `name` | string | 项目名称 |
| `root_path` | string | 根路径 |
| `total_files_estimated` | number | 估算总文件数 |
| `total_files_scanned` | number | 已扫描文件数 |
| `coverage_percent` | number | 覆盖百分比 |
| `truncated` | boolean | 是否因上限被截断 |

### modules 字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `name` | string | 模块名称 |
| `path` | string | 相对路径 |
| `language` | string | 主要语言 |
| `entry_files` | string[] | 入口文件列表 |
| `interfaces` | string[] | 接口文件 glob 模式 |
| `tests` | object | 测试信息（存在性、路径、框架） |
| `config_files` | string[] | 配置文件列表 |
| `data_models` | string[] | 数据模型文件列表 |
| `doc_path` | string | 生成的文档路径（CLAUDE.md 或 AGENTS.md，由 output_format 决定） |
| `coverage` | object | 模块级覆盖率 |

### gaps 字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `module` | string | 所属模块路径 |
| `type` | string | 缺口类型：`missing_tests`、`missing_interface`、`missing_data_model`、`missing_api_docs` 等 |
| `description` | string | 缺口描述 |
| `priority` | string | 优先级：`high`、`medium`、`low` |
| `suggested_path` | string | 建议扫描路径 |
| `suggested_action` | string | 建议的修复动作 |
| `effort_estimate` | string | 预估工作量 |

## 新增字段说明 (v2.0)

### scan_config 字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `strategy` | string | 扫描策略：`lightweight`/`standard`/`thorough` |
| `parallel_enabled` | boolean | 是否启用并行扫描 |
| `max_parallel_modules` | number | 最大并行模块数 |
| `timeout_per_module_ms` | number | 单模块超时时间（毫秒） |

### project 扩展字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `description` | string | 项目简短描述 |
| `primary_language` | string | 主要编程语言 |
| `frameworks` | string[] | 检测到的框架列表 |
| `total_lines_scanned` | number | 扫描的总代码行数 |
| `scan_duration_ms` | number | 扫描耗时（毫秒） |
| `scan_start_time` | string | 扫描开始时间 |
| `scan_end_time` | string | 扫描结束时间 |

### quality_scores 字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `overall` | number | 总体质量评分 (0-100) |
| `documentation_completeness` | number | 文档完整性评分 |
| `test_coverage` | number | 测试覆盖率评分 |
| `api_documentation` | number | API 文档质量评分 |
| `configuration_documentation` | number | 配置文档质量评分 |

### modules 扩展字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `framework` | string | 模块使用的框架 |
| `status` | string | 模块状态：`stable`/`beta`/`deprecated`/`experimental` |
| `cli_commands` | string[] | CLI 命令列表 |
| `exported_functions` | string[] | 导出函数文件列表 |
| `events` | string[] | 事件定义文件列表 |
| `hooks` | string[] | Hook 定义文件列表 |
| `protocol_definitions` | string[] | 协议定义文件列表 |
| `config_format` | string | 配置文件格式 |
| `environment_variables` | string[] | 环境变量列表 |
| `database_type` | string | 数据库类型 |
| `orm` | string | ORM 工具 |
| `quality_score` | number | 模块质量评分 (0-100) |
| `dependencies` | object | 依赖分类（runtime/dev/internal） |
| `security` | object | 安全相关信息 |

### tests 扩展字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `test_files_count` | number | 测试文件数量 |
| `estimated_coverage_percent` | number | 估算的测试覆盖率 |

### dependencies 字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `runtime` | string[] | 运行时依赖 |
| `dev` | string[] | 开发依赖 |
| `internal` | string[] | 内部模块依赖 |

### security 字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `sensitive_patterns_found` | number | 检测到的敏感信息模式数量 |
| `auth_required` | boolean | 是否需要认证 |
| `auth_type` | string | 认证类型 |

### frameworks_detected 字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `name` | string | 框架名称 |
| `version` | string | 框架版本 |
| `type` | string | 框架类型：`frontend`/`backend`/`fullstack`/`database`/`testing`/`build` |
| `config_files` | string[] | 配置文件列表 |
| `modules_using` | string[] | 使用该框架的模块列表 |

### ignore_stats 扩展字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `ignored_by_type` | object | 按类型统计的忽略文件数 |

### security_scan 字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `enabled` | boolean | 是否启用安全扫描 |
| `findings` | array | 安全发现列表 |
| `total_findings` | number | 总发现数 |
| `high_severity_count` | number | 高严重程度数量 |
| `medium_severity_count` | number | 中严重程度数量 |
| `low_severity_count` | number | 低严重程度数量 |

### security_scan.findings 字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `type` | string | 发现类型：`api_key`/`password`/`private_key`/`token`/`internal_url` |
| `severity` | string | 严重程度：`high`/`medium`/`low` |
| `file` | string | 文件路径 |
| `line` | number | 行号 |
| `description` | string | 描述 |

### performance_metrics 字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `scan_duration_ms` | number | 扫描总耗时（毫秒） |
| `modules_per_second` | number | 每秒处理模块数 |
| `files_per_second` | number | 每秒处理文件数 |
| `memory_peak_mb` | number | 内存峰值（MB） |
| `tool_calls_count` | number | 工具调用总次数 |
| `tool_calls_failed` | number | 失败的工具调用次数 |
| `retry_count` | number | 重试总次数 |

### failed_modules 字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `path` | string | 模块路径 |
| `error` | string | 错误信息 |
| `error_type` | string | 错误类型：`file_access`/`timeout`/`encoding`/`other` |
| `retry_count` | number | 重试次数 |
| `last_attempt` | string | 最后尝试时间 |

### file_hashes 字段

类型：`object`

键值对结构，键为文件相对路径，值为文件内容的 hash 值（用于增量更新检测）。

## 版本迁移

### 从 v1.0 到 v2.0

新增字段：
- `scan_config` - 扫描配置
- `quality_scores` - 质量评分
- `frameworks_detected` - 检测到的框架
- `security_scan` - 安全扫描结果
- `performance_metrics` - 性能指标
- `failed_modules` - 失败模块记录
- `file_hashes` - 文件 hash（用于增量更新）

模块字段扩展：
- `framework` - 框架
- `status` - 状态
- `cli_commands` - CLI 命令
- `exported_functions` - 导出函数
- `events` - 事件
- `hooks` - Hook
- `protocol_definitions` - 协议定义
- `config_format` - 配置格式
- `environment_variables` - 环境变量
- `database_type` - 数据库类型
- `orm` - ORM
- `quality_score` - 质量评分
- `dependencies` - 依赖分类
- `security` - 安全信息

测试字段扩展：
- `test_files_count` - 测试文件数
- `estimated_coverage_percent` - 估算覆盖率

缺口字段扩展：
- `suggested_action` - 建议动作
- `effort_estimate` - 工作量预估

## 使用示例

### 读取索引

```javascript
const fs = require('fs');
const index = JSON.parse(fs.readFileSync('.claude/index.json', 'utf-8'));

// 获取项目概览
console.log(`项目: ${index.project.name}`);
console.log(`模块数: ${index.modules.length}`);
console.log(`覆盖率: ${index.project.coverage_percent}%`);

// 获取质量评分
console.log(`质量评分: ${index.quality_scores.overall}/100`);

// 获取高优先级缺口
const highPriorityGaps = index.gaps.filter(g => g.priority === 'high');
console.log(`高优先级缺口: ${highPriorityGaps.length} 个`);
```

### 增量更新检查

```javascript
function needsUpdate(index, modulePath) {
  const module = index.modules.find(m => m.path === modulePath);
  if (!module) return true;

  // 检查文件 hash 变化
  const currentHash = calculateHash(modulePath);
  return index.file_hashes[modulePath] !== currentHash;
}
```
