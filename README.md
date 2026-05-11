# hpu-exec

A high-performance unload script for extracting DB2 DPF (Data Partitioning Feature) data directly from outside the DB2 engine in MPP (Massively Parallel Processing) environments.

## Overview

`hpu-exec` leverages `db2hpu` to perform parallel data unloads across multiple database partitions and physical hosts. It coordinates SSH-based distributed execution to maximize throughput.

## Prerequisites

Create the tracking table before use:

```sql
CREATE TABLE dba.hpu_unload (
    domain VARCHAR(32),
    tabschema VARCHAR(128),
    tabname VARCHAR(128),
    hpu_run_count SMALLINT,
    hpu_success_count SMALLINT,
    hpu_fail_count SMALLINT,
    spark_hpu_run_count SMALLINT,
    spark_hpu_success_count SMALLINT,
    spark_hpu_fail_count SMALLINT
);
```

## Usage

```bash
./hpu-exec <function> [arguments]
```

Run without arguments to list available functions.

## Functions


| Function | Description | Arguments |
| :--- | :--- | :--- |
| `list_hosts` | List all physical hosts with logical node numbers | - |
| `list_data_hosts` | List hosts containing data for a table | `<schema> <table>` |
| `list_data_nodes_info` | List node info for a table | `<schema> <table>` |
| `list_data_current_nodes` | List current host's nodes for a table | `<schema> <table>` |
| `unload_on_all_nodes` | Run parallel unload across all data hosts | `<schema> <table>` |
| `unload_on_local_node` | Run unload on local host only | `<schema> <table>` |
| `show_hpu_proc` | Show running hpu processes across hosts | `[filter]` |

## Configuration


| Variable | Default | Description |
| :--- | :--- | :--- |
| `OUTPATH` | `/scratch/temp` | Output directory for unloaded data |
| `PIPEPATH` | `/tmp` | Directory for named pipes |
| `UNLOAD_PARALLELISM` | `2` | Max concurrent unloads |

## Output

Data is exported as gzip-compressed, caret (`^`) delimited files:

`<OUTPATH>/<schema>_<table>-<hostname>.gz`

## Example

```bash
# List all hosts
./hpu-exec list_hosts

# Unload a table across all nodes
./hpu-exec unload_on_all_nodes MYSCHEMA MYTABLE
```
