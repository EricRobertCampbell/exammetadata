# exammetadata v0.2

Companion metadata exporter for the LaTeX `exam` class; works with pdfLaTeX (no LuaTeX).

Licensed under the [GNU General Public License v3](LICENSE).

## Installation

Copy `exammetadata.sty` somewhere LaTeX can find it, then use `\usepackage{exammetadata}`.

**Project-local (simplest):** put `exammetadata.sty` in the same directory as your `.tex` file.

**Personal texmf tree (reusable):**

```bash
mkdir -p ~/texmf/tex/latex/exammetadata
cp exammetadata.sty ~/texmf/tex/latex/exammetadata/
```

TeX Live usually finds `~/texmf` automatically. If not, run `texhash ~/texmf`.

Requires a TeX Live install with the `exam` class, `expl3`, and `xparse` (standard on recent TeX Live).

## Build

Run `pdflatex example.tex` twice. The PDF is the ordinary exam output; `example.questions.tsv` is the metadata export.

## Commands

- `\AssessmentSetup{id=...,version=...}` in the preamble (required; the package errors if omitted).
- `\mquestion[marks]{id=...,outcomes={...},difficulty=...,type=...,notes={...}}`
- `\mpart[marks]{outcomes={...},difficulty=...,type=...,notes={...}}`
- `\msubpart[marks]{...}`
- `\msubsubpart[marks]{...}`
- `\mchoice{notes={...}}` and `\mcorrectchoice{notes={...}}` inside `choices` or `oneparchoices`.

All marks arguments are optional; use them only for mark-bearing leaf items. A parent question/part containing marked children should omit its own points. Metadata values `outcomes`, `difficulty`, and `type` inherit down the question/part hierarchy. Notes never inherit. Choices inherit question metadata and emit zero-mark rows with `correct=true/false`.

A bare `\question`, `\part`, `\subpart`, `\subsubpart`, `\choice` or `\CorrectChoice` still works for typesetting but is **not exported**. Use the `m` wrappers to export metadata. Question and part notes are only emitted when the corresponding item has marks. Unmarked parent notes are not exported in v0.2.

## TSV fields

`assessment`, `version`, `question`, `id`, `kind`, `path`, `marks`, `outcomes`, `difficulty`, `type`, `notes`, `correct`.

`kind` is `question`, `part`, `subpart`, `subsubpart`, or `choice`. `path` identifies a nested item (`b.ii.a`), or the choice letter (`B`). The question number and ID identify the parent question. For mark aggregation, include only records with nonblank `marks` and ignore choice records.

## Caveats

- The package is a prototype and does not automatically detect unexported ordinary `exam` commands, enforce marks consistency, or validate curriculum outcome codes.
- Metadata should be plain text. Braces protect commas in `notes`; avoid literal tabs, newlines and LaTeX commands in metadata, since the exporter writes plain TSV without full quoting/escaping.
- Question IDs should be unique within an assessment/version.
- `\mchoice` and `\mcorrectchoice` emit records only when used; unwrapped choices are not recorded.
- Recompile after edits; don't rely on a partial TSV after a failed build.
