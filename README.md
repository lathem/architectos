# ArchitectOS (AOS)

**ArchitectOS (AOS)** is an AI-native development automation framework.

It treats your codebase as a *living system*, with three main pieces:

1. **Declarative project spec** (`project.yml` / `.aos.yml`)  
   - Describes the project’s structure, build steps, test suite, and deployment targets.  
   - Acts as a *single source of truth* between humans, CI, and LLM copilots.

2. **Sandboxed build runner**  
   - Spins up isolated environments (containers, chroots, or local sandboxes).  
   - Runs compilers, linters, tests, and packaging commands reproducibly.  
   - Can be triggered by humans, CI, or an LLM toolchain.

3. **LLM bridge** (future)  
   - Structured protocols for letting an LLM propose file edits, refactors, and migrations.  
   - Changes are validated by the sandbox (lint/tests) before being accepted.  
   - Errors and logs are fed back to the LLM for iterative fixes.

ArchitectOS is designed to help teams:

- Modernize legacy codebases
- Automate repetitive devops/build chores
- Safely integrate LLMs into real projects without giving them direct shell access

---

## Status

This repo currently includes:

- The **ArchitectOS whitepaper**
- Initial **contributor guidelines**
- A minimal **docs site** (GitHub Pages–ready)

Source code for the core `aos` runner and modules will be added in upcoming versions.

---

## Docs

See the docs bundle in:

- `architectos-docs-bundle-0.1.1/`

Key files:

- `WHITEPAPER-ArchitectOS.md` – Concept, architecture, and motivation  
- `CONTRIBUTING.md` – How to get involved as the project evolves  
- `docs/index.md` – Landing page for the documentation site

---

## License

ArchitectOS is licensed under the **Apache License 2.0**.  
See [`LICENSE`](architectos-docs-bundle-0.1.1/LICENSE) for details.
