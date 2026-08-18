# HYN-Skill

HYN-Skill is HYN's personal project workflow and initialization Skill. It helps a new project establish useful context, project-specific Agent rules, documentation entry points, decision records, and verification guidance before the project becomes difficult to understand.

It is intentionally conservative: it previews first, creates only missing files, preserves existing project content, and keeps personal preferences separate from shared project rules.

## Directory layout

```text
HYN-Skill/
├── SKILL.md
├── agents/openai.yaml
├── references/
├── scripts/project_foundation.py
├── assets/
└── tests/
```

`SKILL.md` is the short routing entrypoint. Detailed guidance is in `references/`, deterministic behavior is in `scripts/`, and generated project templates are in `assets/`.

## Manual installation

This delivery does not install itself automatically. To install the unpacked Skill into Codex, copy or extract the directory under the personal Skill directory and use the technical name for the destination:

```bash
mkdir -p ~/.codex/skills
cp -a /path/to/HYN-Skill ~/.codex/skills/hyn-skill
```

The generated frontmatter name is `hyn-skill`; the UI display name is `HYN-Skill`.

## Basic usage

Use natural language such as:

```text
使用 HYN-Skill 初始化这个项目，先预览，再应用。
```

The deterministic helper can also be used directly from this directory:

```bash
python3 scripts/project_foundation.py inspect --project /path/to/project
python3 scripts/project_foundation.py init --project /path/to/project
python3 scripts/project_foundation.py init --project /path/to/project --apply
python3 scripts/project_foundation.py validate-config --config /path/to/project/.hyn-skill.toml
```

Initialization defaults to preview mode. `--apply` is required to write files.

## Generated project layers

- `README.md`: human-facing project entry point.
- `AGENTS.md`: current project's commands, conventions, architecture constraints, and safety rules.
- `CONTEXT.md`: current facts, vocabulary, ownership, boundaries, and open questions.
- `docs/`: stable decisions and verification records.
- `learn/`: cross-project knowledge and not-yet-stable learning notes.
- `docs/archive/`: important historical states only, not an automatic activity log.

HYN-Skill does not copy the personal Skill into `AGENTS.md`. The project file remains standalone and shareable.

## Configuration

Global preferences are optional and live outside this Skill:

```text
$XDG_CONFIG_HOME/hyn-skill/preferences.toml
```

If `XDG_CONFIG_HOME` is unset, the fallback is `~/.config/hyn-skill/preferences.toml`.

Project-specific overrides live at the project root:

```text
.hyn-skill.toml
```

The precedence is:

```text
Skill defaults < detected profile < HYN global preferences < project configuration < explicit user/CLI instruction
```

Minimal configuration:

```toml
schema_version = 1
profile = "auto"
level = "standard"
language = "auto"
history = "material-change"
```

Supported profiles are `generic`, `python`, `cpp`, `typescript-web`, `ai-agent`, and `docs`. Profiles only influence detection, documentation emphasis, and command discovery; they do not generate framework code.

## Initialization levels

| Level      | Generated foundation                                         |
| ---------- | ------------------------------------------------------------ |
| `lean`     | `README.md`, `CONTEXT.md`, `docs/README.md`                  |
| `standard` | `lean` plus `AGENTS.md`, decision index, verification index, and `learn/README.md` |
| `deep`     | `standard` plus glossary, archive index, and `learn/map.md`  |

Existing files are never overwritten. If an important file already exists, the script skips it and reports the missing or recommended content instead.

## Extending project types

Add a new profile entry to `assets/profiles.toml`, including its observable markers, source suffixes, focus areas, and suggested level. Update the profile reference and tests. Do not add a profile-specific architecture rule to the core `SKILL.md` unless it is genuinely cross-project.

## Validation

From the Skill root:

```bash
python3 -m unittest discover -s tests -v
python3 /path/to/skill-creator/scripts/quick_validate.py .
```

The official Skill Creator bundle validates frontmatter and unfinished scaffolding. The tests in this Skill validate project detection, configuration precedence, safe/idempotent initialization, templates, and ZIP-ready contents.

## Packaging

The official local Skill Creator provides initialization, metadata generation, and validation. It does not provide a separate packaging command, so package the validated directory with a standard ZIP command while excluding Python caches and temporary files:

```bash
cd /path/to/parent
zip -r -X HYN-Skill.zip HYN-Skill \
  -x 'HYN-Skill/__pycache__/*' 'HYN-Skill/**/*.pyc' 'HYN-Skill/.pytest_cache/*'
unzip -t HYN-Skill.zip
```
