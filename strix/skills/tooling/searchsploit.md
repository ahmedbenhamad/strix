---
name: searchsploit
description: Searchsploit CLI for querying Exploit-DB, used as fallback to PentestRag
---

# Searchsploit Playbook

## Installation

```bash
apt-get install exploitdb
```

## Basic Usage

### Search by keyword
```bash
searchsploit --json Apache 2.4.49
```

### Search by CVE
```bash
searchsploit --json CVE-2021-41773
```

### Filter by platform
```bash
searchsploit --json --platform linux OpenSSL
```

### Update database
```bash
searchsploit -u
```

## Output Format

JSON output includes:
- `path`: Local path to exploit file
- `title`: Exploit title
- `edb_id`: Exploit-DB ID
- `platform`: Target platform (Windows, Linux, Web)
- `type`: Exploit type (RCE, XSS, SQLi, etc.)
- `author`: Exploit author

## Usage in Agents

### Query Searchsploit
```python
from strix.tools.exploit_intelligence import SearchsploitClient

client = SearchsploitClient()
exploits = client.search_service("Apache", "2.4.49")

for e in exploits:
    print(f"EDB-{e.edb_id}: {e.title}")
    print(f"  URL: {e.get_edb_url()}")
```

### When to Use

- PentestRag unavailable or limited results
- Older vulnerabilities with broad EDB coverage
- Public exploit validation and cross-reference
- Author verification and community ratings

## Caching

Results cached in `/workspace/exploit_cache/searchsploit/{keyword}_{platform}.json`
