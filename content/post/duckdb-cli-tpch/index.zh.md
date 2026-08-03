---
title: "DuckDB CLI 安装与 TPC-H 插件配置"
description: "在 Windows 上安装 DuckDB CLI，配置 tpch 插件并生成测试数据。"
date: "2026-08-01"
slug: "duckdb-cli-tpch"
categories: [数据工具]
tags: ["DuckDB","SQL","数据分析"]
image: "/img/article-covers/duckdb-cli-tpch.svg"
toc: true
comments: false
license: false
---
> 本文根据个人使用记录整理。软件版本、命令和第三方服务可能变化；涉及账号、密钥或服务地址时，请替换为你自己的配置，不要公开真实凭据。

## DuckDB CLI 安装与 tpch 插件配置

### 1. 下载 DuckDB CLI

**Step 1** — 在 D 盘创建目录：

```
D:\tools\duckdb
```

**Step 2** — 打开浏览器，到 DuckDB GitHub Releases 页面：

> [https://github.com/duckdb/duckdb/releases/tag/v1.5.2](https://github.com/duckdb/duckdb/releases/tag/v1.5.2)

向下滚动到 **Assets**，根据自己的系统架构下载对应文件：

| 架构 | 下载文件 |
|------|---------|
| Intel/AMD（x86_64） | `duckdb_cli-windows-amd64.zip` |
| ARM（如 Surface Pro X） | `duckdb_cli-windows-arm64.zip` |

> 如何查看自己电脑的架构：
> 打开 PowerShell，输入 `echo $env:PROCESSOR_ARCHITECTURE`
> 输出 `AMD64` 选 amd64 版，输出 `ARM64` 选 arm64 版

**Step 3** — 解压 `duckdb_cli-windows-*.zip`，把里面的 `duckdb.exe` 放到 `D:\tools\duckdb\`

**Step 4** — 验证安装

```powershell
D:\tools\duckdb\duckdb.exe
```

看到类似下面的信息就成功了：

```
DuckDB v1.5.2 (Variegata)
Enter ".help" for usage hints.
memory D
```

输入 `.quit` 退出。

**Step 5（可选）— 添加到 PATH**

如果想在任何路径下直接打 `duckdb` 就能启动：

1. `Win + R` → 输入 `sysdm.cpl`
2. **高级** → **环境变量**
3. **用户变量** 中找到 `Path` → **编辑** → **新建** → 输入 `D:\tools\duckdb` → **确定**

### 2. 进入 DuckDB

**方式一：未添加 PATH**

每次打开 PowerShell，直接输入：

```powershell
D:\tools\duckdb\duckdb.exe
```

**方式二：已添加 PATH**

重新打开 PowerShell 终端，输入：

```powershell
duckdb
```

进入后出现如下提示符，表示已进入 DuckDB 命令行：

```
memory D
```

输入 SQL 语句即可执行，输入 `.quit` 退出。

### 3. 安装 tpch 插件

进入 DuckDB 后依次执行：

```sql
INSTALL tpch;
LOAD tpch;
```

然后生成测试数据验证：

```sql
CALL dbgen(sf = 0.01);
.tables
```

能看到 8 张表（customer、lineitem、nation、orders、part、partsupp、region、supplier）即成功。

> `sf`（Scale Factor）参数控制数据量：
> - `0.01` ≈ 10MB，适合快速测试
> - `0.1` ≈ 100MB
> - `1` ≈ 1GB（标准 TPC-H 基准规模）

### 4. tpch 插件常用命令速查

| 命令 | 作用 |
|------|------|
| `CALL dbgen(sf = 0.1);` | 按比例因子生成测试数据 |
| `CALL tpch_queries();` | 列出 22 条标准 TPC-H 查询 |
| `CALL tpch_query(1);` | 执行第 N 条 tpch 查询 |
| `.tables` | 查看所有已生成的表 |
| `DESCRIBE lineitem;` | 查看表结构 |
| `SELECT count(*) FROM orders;` | 查看某张表的数据量 |

### 5. 快速上手示例

进入 DuckDB 后：

```sql
INSTALL tpch;
LOAD tpch;
CALL dbgen(sf = 0.01);

-- 查看有哪些表
.tables

-- 跑一条分析查询（找出订单最多的前 5 个客户）
SELECT c_name, count(*) AS order_count
FROM customer
JOIN orders ON c_custkey = o_custkey
GROUP BY c_name
ORDER BY order_count DESC
LIMIT 5;
```

---

*教程版本：v1.2 | 适用 DuckDB 1.5.2 | 2026-06-18*
