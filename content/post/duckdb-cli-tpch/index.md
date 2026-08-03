---
title: "DuckDB CLI Installation and TPC-H Plugin Setup"
description: "Install DuckDB CLI on Windows, configure the tpch extension, and generate test data."
date: "2026-08-01"
slug: "duckdb-cli-tpch"
categories: [数据工具]
tags: ["DuckDB","SQL","Data Analysis"]
image: "/img/article-covers/duckdb-cli-tpch.svg"
toc: true
comments: false
license: false
---
> Compiled from personal usage notes. Software versions, commands, and third-party services may change; when credentials or service addresses are involved, replace them with your own configuration and never publish real credentials.

## Installing DuckDB CLI and configuring the tpch extension

### 1. Download the DuckDB CLI

**Step 1** — Create a directory on your D: drive:

```
D:\tools\duckdb
```

**Step 2** — Open your browser and go to the DuckDB GitHub Releases page:

> [https://github.com/duckdb/duckdb/releases/tag/v1.5.2](https://github.com/duckdb/duckdb/releases/tag/v1.5.2)

Scroll down to **Assets** and download the file that matches your system architecture:

| Architecture | File to download |
|------|---------|
| Intel/AMD (x86_64) | `duckdb_cli-windows-amd64.zip` |
| ARM (e.g. Surface Pro X) | `duckdb_cli-windows-arm64.zip` |

> How to check your machine's architecture:
> Open PowerShell and run `echo $env:PROCESSOR_ARCHITECTURE`
> If it prints `AMD64`, choose the amd64 build; if `ARM64`, choose the arm64 build.

**Step 3** — Unzip `duckdb_cli-windows-*.zip` and place `duckdb.exe` into `D:\tools\duckdb\`

**Step 4** — Verify the installation

```powershell
D:\tools\duckdb\duckdb.exe
```

You should see something like this:

```
DuckDB v1.5.2 (Variegata)
Enter ".help" for usage hints.
memory D
```

Type `.quit` to exit.

**Step 5 (optional) — Add to PATH**

To launch `duckdb` from any directory:

1. Press `Win + R` → enter `sysdm.cpl`
2. **Advanced** → **Environment Variables**
3. Find `Path` in **User variables** → **Edit** → **New** → enter `D:\tools\duckdb` → **OK**

### 2. Launching DuckDB

**Option A: PATH not added**

In PowerShell, run:

```powershell
D:\tools\duckdb\duckdb.exe
```

**Option B: PATH added**

Open a new PowerShell terminal and run:

```powershell
duckdb
```

You are in the DuckDB shell once you see this prompt:

```
memory D
```

Run SQL statements directly; type `.quit` to exit.

### 3. Installing the tpch extension

Inside DuckDB, run:

```sql
INSTALL tpch;
LOAD tpch;
```

Then generate test data to verify:

```sql
CALL dbgen(sf = 0.01);
.tables
```

Success means you can see 8 tables (customer, lineitem, nation, orders, part, partsupp, region, supplier).

> The `sf` (Scale Factor) parameter controls data volume:
> - `0.01` ≈ 10MB, good for quick tests
> - `0.1` ≈ 100MB
> - `1` ≈ 1GB (standard TPC-H benchmark scale)

### 4. tpch extension quick reference

| Command | Purpose |
|------|------|
| `CALL dbgen(sf = 0.1);` | Generate test data at a given scale factor |
| `CALL tpch_queries();` | List the 22 standard TPC-H queries |
| `CALL tpch_query(1);` | Run the Nth TPC-H query |
| `.tables` | List all generated tables |
| `DESCRIBE lineitem;` | Show table schema |
| `SELECT count(*) FROM orders;` | Check row count of a table |

### 5. Quick start example

Inside DuckDB:

```sql
INSTALL tpch;
LOAD tpch;
CALL dbgen(sf = 0.01);

-- List all tables
.tables

-- Run an analytical query (top 5 customers by number of orders)
SELECT c_name, count(*) AS order_count
FROM customer
JOIN orders ON c_custkey = o_custkey
GROUP BY c_name
ORDER BY order_count DESC
LIMIT 5;
```

---

*Guide version: v1.2 | Applies to DuckDB 1.5.2 | 2026-06-18*
