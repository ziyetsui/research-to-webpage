---
name: research-to-webpage
description: Two-step workflow for turning reference projects, docs, repos, or product ideas into reusable research briefs and then runnable static atlas/catalogue webpages. Use when the user asks to research a repo or docs, abstract a reusable model, create markdown research documents, split research from implementation, implement a webpage from a reference repo, create design tokens/registries/static galleries, create or name a GitHub repo, or prepare Vercel/static deployment.
---

# Research To Webpage

Use this skill for a two-step system:

```text
@research -> markdown research documents
@webpage  -> runnable webpage/repo/deployable static site
```

Keep the boundary clear:

- **Research** reads sources and outputs `.md` documents only.
- **Webpage** turns research docs and the ref implementation model into code, registry, tokens, repo, and deployment config.

## Step 1: Research Mode

Use research mode when the user asks to read docs/repos, abstract a model, compare coverage, make handoff notes, or produce a reusable methodology.

Load `references/research.md` for the full protocol. If the user asks for the thinking method itself, also load `references/research-method.md`.

Research mode must produce Markdown artifacts, commonly:

```text
docs/RESEARCH_BRIEF.md
docs/SYSTEM_MODEL.md
docs/IMPLEMENTATION_BRIEF.md
docs/COVERAGE_REPORT.md
```

Do not implement website code, create GitHub repos, or deploy in research mode. If implementation is requested, either ask to switch to webpage mode or proceed with webpage mode if the user explicitly asks for both steps.

## Step 2: Webpage Mode

Use webpage mode when the user asks to implement the site, create a static atlas/catalogue, build the webpage, generate tokens/registry, create a repo, or prepare deployment.

Load:

```text
references/webpage.md
references/reference-repo-implementation-model.md
```

Default stack:

```text
Static HTML + CSS + Vanilla JS + JSON registries + Python stdlib build script + Vercel static hosting
```

Default implementation flow:

1. Read research docs or quickly create an implementation brief if none exist.
2. Choose a stable kebab-case repo/project name when the user has not specified one.
3. Create the static site skeleton: `README.md`, `AGENTS.md`, `docs/`, `agents/`, `site/`, `scripts/`, `assets/`, and domain-specific entry folders.
4. Implement frontmatter/spec files, registry JSON, validation, gallery, viewer, copy actions, design tokens, and proof artifacts.
5. Add `vercel.json` and local run/deploy commands.
6. If requested, use the current `gh` account to create a private GitHub repo and push `main`.
7. Validate with build scripts and browser checks for frontend work.

## GitHub Defaults

When creating a repo:

- Use the active `gh` account unless the user specifies otherwise.
- Default to private.
- Agent may choose the repo name.
- Use stable kebab-case names such as `<domain>-atlas`, `<product>-style-lab`, `<subject>-reference-system`, or `<thing>-catalogue`.
- Check that the repo name is available.
- Check for files over GitHub's 100MB limit.
- Report URL, visibility, branch, and commit.

## Quality Rules

- Do not invent missing sources; mark missing files explicitly.
- Preserve code paths, file names, IDs, commands, registry keys, and token names verbatim.
- Keep generated skills and agent protocols in Markdown unless executable automation is truly needed.
- Use registry-driven UI rather than hand-maintained duplicate cards.
- Make design tokens explicit contracts.
- Prefer no-framework static output unless the user requests a framework.
- For frontend deliverables, run validation and inspect the result in a browser when feasible.

## References

- `references/research.md` - full `@research` protocol.
- `references/research-method.md` - compact human-readable research method.
- `references/webpage.md` - full `@webpage` implementation protocol.
- `references/reference-repo-implementation-model.md` - technical stack, implementation principle, design tokens, repo, and deployment model from the ref repo.
