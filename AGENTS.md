# ml-wisdom-skills

Public Agent Skills pack for ML/DL **judgment** (not Hub/CLI tool docs).

## Before editing skills

1. Read `GLOSSARY.md` for domain language.
2. Read `docs/DESIGN.md` and `docs/adr/` for locked decisions.
3. Keep each `skills/<name>/SKILL.md` concise; put depth in `references/` (skill-local or repo `references/`).
4. `name` in frontmatter must match the directory name.
5. Cite sources in `references/sources.md`; do not duplicate Hugging Face / Ultralytics / K-Dense tool skills.

## Catalog shape

Spine skills are **verb-named** workflow actions (`audit-data`, `verify-training`, …). CV overlays use `cv-*`. Geo is out of scope for v1.
