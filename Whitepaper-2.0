
# ArchitectOS Whitepaper v2.0
### *A Multi-LLM Deterministic Development Environment for Automated Refactoring, Build Systems, and Code Repair*
**Version:** 0.3.6  
**Date:** 2025  

## Abstract

ArchitectOS (AOS) is a deterministic, domain-governed development automation framework that integrates large language models (LLMs) directly into software engineering workflows. ArchitectOS provides reproducible builds, controlled system access for LLM-driven automation, a unified multi-provider LLM interface, and a project-centric toolchain system. All actions taken by or through the LLM occur within strict execution domains: **SAFE**, **NET**, or **UNBOUND**, each with explicit behavioral and security guarantees.

Version **0.3.6** introduces a significant architectural milestone: the **multi-provider LLM orchestration layer**. This layer adds support for OpenAI (default), Anthropic Claude, Google Gemini, DeepSeek, Qwen (Alibaba DashScope), and Cohere Command-R through a vendor-agnostic API abstraction. Providers are auto-registered via a plugin-like registry, allowing AOS to swap LLM backends instantly without modifying project code.

This whitepaper describes the current architecture, rationale, domain model, multi-LLM subsystem, toolchain manager, RAG skeleton, installer/upgrade pipeline, and reproducibility guarantees of ArchitectOS as implemented in version 0.3.6.

---

# 1. Introduction

Modern AI-assisted development tooling lacks determinism, safety boundaries, and reproducible environments. LLMs often suggest changes to codebases without direct knowledge of the underlying filesystem, or perform reasoning in isolation from the actual project context.

ArchitectOS solves these problems by:

- Providing a **deterministic execution environment** for LLMs  
- Organizing all operations into governed modes with explicit permission domains  
- Allowing seamless use of multiple LLM providers  
- Guaranteeing reproducibility across builds and deployments  
- Bundling installers, upgrade scripts, and repair utilities  

With the release of version **0.3.6**, ArchitectOS becomes the first development automation framework engineered to be *vendor-independent*, *deterministic*, and *LLM-native*.

---

# 2. System Overview

ArchitectOS is structured around five architectural pillars:

1. **Deterministic Execution Domains (SAFE, NET, UNBOUND)**  
2. **Project-Centric Architecture & Tooling**  
3. **Multi-Provider LLM Integration Layer**  
4. **Controlled OS Bridge for System-Level Operations**  
5. **Reproducible Toolchains & First-Run Provisioning**

Each of these components is fully implemented and functional in the 0.3.6 release.

---

# 3. Execution Domains

ArchitectOS defines strict execution boundaries to prevent unintended side effects from LLM-driven operations.

| **Domain** | **Description** | **Permissions** |
|------------|-----------------|-----------------|
| **SAFE** | Deterministic, auditable mode. Default for most operations. | No shell, no arbitrary FS writes, no network. |
| **NET** | SAFE + controlled networking. Used for RAG and package installs. | Network allowed; still no shell. |
| **UNBOUND** | Fully elevated operation. “PFY mode.” | Shell, FS, network, SSH; all logged. |

Domain enforcement is handled in `cli.py`, `mode_manager.py`, and wrapper scripts such as `aos-unbound`.

---

# 4. Mode Engine

Modes give semantic meaning to LLM actions and map directly to execution domains.

| **Mode** | **Purpose** | **Domain** |
|----------|-------------|------------|
| `architect` | High-level planning | SAFE |
| `spec` | Generate/modify project.yml | SAFE |
| `refactor` | Controlled file patching | SAFE |
| `build` | Validators / tests | SAFE |
| `diagnostic` | Error parsing & remediation | SAFE |
| `rag` | Retrieval augmented analysis | NET |
| `toolchain` | Manage runtimes & venvs | NET |
| `os_bridge` | System introspection | SAFE |
| `unbound` | User-directed full control | UNBOUND |

These mappings reflect the current implementation in `mode_manager.py`.

---

# 5. Deterministic OS Bridge

The OS bridge is designed to prevent hallucinations from causing system damage. It provides:

- Controlled file patch operations  
- Deterministic environment checks  
- Forbidden arbitrary shell access in SAFE/NET  
- Full logging in UNBOUND  

AOS ensures that dangerous or nondeterministic actions cannot occur unless explicitly promoted to UNBOUND mode.

---

# 6. Project Structure

ArchitectOS uses a reproducible per-project directory layout:

```text
project.yml
.architectos/
    constraints.yml
    llm.yml
    toolchain.yml
bin/aos
.venv/
requirements.txt
CHANGELOG.md
docs/specs/
```

The `bin/aos` runner:

- Creates and activates `.venv/`  
- Loads and validates project metadata  
- Dispatches to `architectos.cli`  

This ensures all behavior is project-local and environment-locked.

---

# 7. Toolchain Manager

ArchitectOS includes a toolchain manager defined in `toolchain_manager.py`.

### Features

- Per-language toolchain directories  
- Metadata stored in:
  - `meta.json`
  - `status.json`
- Optional `env/` virtual environment creation  
- Project binding via `.architectos/toolchain.yml`

### Commands

```bash
aos toolchain list
aos toolchain create python mytc --python /usr/bin/python3.11
aos toolchain use python mytc
```

Toolchains live under:

```text
~/.architectos/toolchains/<language>/<name>/
```

---

# 8. First-Run Wizard

The first-run wizard (`maybe_run_first_run_wizard()`) initializes user environments.

### Trigger conditions

- No existing toolchains  
- No `~/.config/architectos/first_run_done`  
- Interactive TTY present  

### Behavior

- Prompts user to create a default Python toolchain  
- Creates `py-default` toolchain  
- Optionally binds it if a `project.yml` exists  
- Skipped in CI/non-interactive sessions  
- Controlled by:
  - `AOS_FIRST_RUN_AUTO=1`
  - `AOS_NO_WIZARD=1`

---

# 9. Installer, Upgrade & Repair System

ArchitectOS ships with:

- `install.sh` – systemwide or user-level install  
- `aos-upgrade` – replace/activate new versions  
- `aos-doctor` – environment diagnostics  

Installed directory layout:

- User install:  
  `~/.local/share/architectos/<version>/`  
- System install:  
  `/opt/architectos/<version>/`

A symlink `current/` always points to the active version.

---

# 10. Retrieval-Augmented Generation (RAG)

Implemented via `rag_cli.py`.  

Provides:

- `aos rag index`
- `aos rag query`
- `aos rag verify`
- `aos rag stats`

These commands are operational stubs, intended for expansion with embeddings, vector databases, and structured retrieval.

---

# 11. Multi-Provider LLM Architecture (0.3.6)

This is the major enhancement of version 0.3.6.  
ArchitectOS now includes a modular, extensible multi-LLM backend.

## 11.1 Components

### LLMConfig

Loaded from `.architectos/llm.yml` and environment variables.

### LLMClient

Routes messages to the correct provider via registry lookup.

### Provider Registry

Located in `architectos/llm_providers/__init__.py`.

### Base Provider Class

Defines a common `complete()` interface.

---

## 11.2 Supported Providers

- OpenAI (default)  
- Anthropic Claude  
- Google Gemini  
- DeepSeek (R1)  
- Qwen (DashScope)  
- Cohere Command-R  

All match the active codebase.

---

## 11.3 Provider Selection

Default configuration:

```text
provider: openai
model: gpt-4.1
```

Override via environment or `.architectos/llm.yml`.

---

## 11.4 LLM Test CLI

```bash
aos llm test
```

Validates:

- API key presence  
- Provider wiring  
- Connectivity  

---

# 12. Reproducibility Guarantees

ArchitectOS ensures deterministic operation through locked project environments, mode/domain enforcement, logged UNBOUND actions, stable toolchain metadata, and predictable execution.

---

# 13. Security Model

Security is enforced via:

- Domain boundaries  
- Controlled OS bridge  
- Logged elevation paths  
- Restricted SAFE/NET behavior  

---

# 14. Related Work

ArchitectOS draws inspiration from:

- Reproducible build systems  
- LLM orchestration frameworks  
- AI-powered IDE tooling  
- Containerized development environments  

ArchitectOS differentiates itself via deterministic guarantees and multi-provider LLM governance.

---

# 15. Roadmap

### 0.4.x

- RAG backends  
- Rich validators  

### 0.5.x

- Advanced refactor engine  

### 0.6.x

- Plugin system  
- RescueWP toolkit  

### 1.0

- Consensus multi-model evaluation  
- Continuous AI memory  

---

# 16. Conclusion

ArchitectOS 0.3.6 establishes a new class of LLM-native development environments: deterministic, governed, reproducible, and vendor-agnostic. It provides a robust platform for AI-assisted engineering that operates safely and predictably under strict domain constraints, while supporting rapid evolution of the LLM ecosystem.

---

# Appendix A – Directory Layout

```text
architectos-0.3.6/
  architectos/
    cli.py
    llm_client.py
    llm_providers/
      base.py
      __init__.py
      openai.py
      deepseek.py
      qwen.py
      cohere.py
    toolchain_manager.py
    mode_manager.py
    config.py
  docs/specs/
    llm.md
    toolchains.md
    execution_domains.md
  install.sh
  requirements.txt
  README.md
  CHANGELOG.md
```
