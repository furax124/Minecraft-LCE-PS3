# Security Analysis Report

**Date:** 2026-03-04  
**Repository:** furax124/Minecraft-LCE-PS3  
**Scope:** Full static analysis of all source files (26,910 files)

## Summary

A comprehensive security review of this repository was performed. **No malicious code was found.**

## Methodology

The following checks were performed:

1. **Script analysis** – All Python (`.py`), shell (`.sh`), batch (`.bat`/`.cmd`), and PowerShell (`.ps1`) files were scanned for dangerous patterns including:
   - Reverse shell payloads (`/dev/tcp`, `nc -e`, `bash -i >&`)
   - Code execution via `eval`, `exec`, `os.system`, `subprocess`
   - Remote downloads (`wget`, `curl`, `urllib`, `requests`)
   - Obfuscated payloads (base64-encoded strings, encoded shellcode)

2. **Source code analysis** – All C/C++ (`.cpp`/`.h`/`.c`) and C# (`.cs`) files were scanned for:
   - Process injection techniques (`CreateRemoteThread`, `WriteProcessMemory`)
   - Windows registry manipulation (`RegOpenKey`, `RegSetValue`)
   - Credential harvesting
   - Hardcoded malicious URLs or IP addresses

3. **Binary file inspection** – All DLL, `.so`, ELF, and `.bin` files were inspected with `strings` for malicious indicators (miner strings, C2 URLs, reverse shell markers).

4. **Build file review** – Makefiles and CMakeLists were reviewed for external command execution or unexpected downloads during the build process.

5. **Static analysis** – `bandit` was run against all Python files for security issues.

6. **Network endpoint review** – All URLs embedded in source or binaries were verified as legitimate (Sony PSN, Facebook Graph API, Khronos, RakNet/Rackspace XML API templates, and archive.org).

## Findings

### No Malicious Code Detected

The repository contains what appears to be leaked source code for **Minecraft: Legacy Console Edition** from 4chan (February 28, 2026), along with older snapshots from 2013 and 2014. No malicious modifications were found.

### Low-Severity Issues (Non-Malicious)

The `bandit` scan flagged the following low-severity issues in the Minecraft Pi Edition Python API files — none of these are malicious:

| File | Issue | Severity |
|------|-------|----------|
| `mc-cpp-snapshot/.../game_snake.py` | Use of `random` (not cryptographically secure) | Low |
| `mc-cpp-snapshot/.../kbhelp.py` | `try/except/pass` block | Low |
| `mc-cpp-snapshot/.../vec3.py` | Use of `assert` in test function | Low |

### Legitimate Code Patterns

The following patterns, which can sometimes appear suspicious, were verified as legitimate:

- **`socket` usage in `api.py`**: Connects to `localhost:4711` – this is the Minecraft Pi Edition Python API port.
- **`eval` in `vec3.py`**: Used in a test function only to round-trip `repr()` output (`eval(repr(it))`).
- **`VirtualAlloc` in `rdlmalloc.h`**: Part of the RakNet memory allocator library – standard usage.
- **`VirtualAlloc` in `PsVitaStubs.cpp`**: A compatibility stub emulating Win32 API for the PS Vita SDK – expected.
- **URLs in `eboot.bin`**: `rscloud.online.scee.com` (Sony), `graph.facebook.com` (Facebook login) – standard game services.
- **Torrent file**: Points to `archive.org` – the Internet Archive (legitimate).

## ⚠️ Legal Notice

While no malicious code was found, this repository contains **leaked proprietary source code** from Mojang/Microsoft. The repository owner explicitly disclaims responsibility. Users should be aware of potential copyright and intellectual property issues when interacting with this code.
