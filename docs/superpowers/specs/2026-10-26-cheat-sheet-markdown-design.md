# AWS Cheat Sheet Markdown Repository Design

Date: 2026-10-26

## Objective

Convert the existing Notion HTML cheat sheet export into an English Markdown repository that can be uploaded to GitHub. Preserve its substantive knowledge and useful illustrations, organize content by service or topic, and incorporate knowledge from practice questions without repetition.

## Source inventory

The source is `Private & Shared/` in the workspace. It contains 98 HTML pages: one root index, 74 top-level service or topic pages, and 23 nested pages. There are 214 image files: 212 PNG files and two SVG files. Multiple pages can reference an image, so image-reference counts differ from image-file counts. The extracted page text is approximately 293,470 characters before translation and consolidation.

The source includes Vietnamese and English prose, tables, nested lists, code blocks, diagrams, and question screenshots. Some services have multiple source pages, including AWS Glue. The workspace also contains the original ZIP export and macOS metadata. It is not initially a Git repository.

## Agreed approach

Use a plain Markdown repository with one folder per service or cross-service topic. Preserve meaningful nested topics as subfolders. The user selected preservation of useful images with English transcription of their textual knowledge, and subsequently requested that question content become knowledge rather than a separate question collection. Additional insights from questions may appear in a Notes section.

No documentation website, build pipeline, or application is required.

## Repository structure

```text
README.md
.gitignore
services/
  ec2/
    README.md
    assets/
  s3/
    README.md
    assets/
  vpc/
    README.md
    public-and-private-subnets/
      README.md
      assets/
docs/
  conversion-report.md
  superpowers/
    specs/
      2026-10-26-cheat-sheet-markdown-design.md
    plans/
```

All authored prose, headings, navigation labels, captions, image descriptions, and output folder names use English. Folder names use lowercase kebab-case. Existing image pixels may contain source-language text; their meaningful text must also be available in English Markdown. Retained source images are not redrawn solely to change their embedded language.

The root README introduces the collection and links to every service and cross-service topic. Each service README contains its main notes and links to any substantive child topics. Child topic READMEs link back to their parent. There are no output `questions/` folders.

## Content conversion

Translate all substantive source prose into natural English while preserving technical meaning, conditions, numerical values, code, commands, and URLs. Preserve headings, lists, emphasis, tables, and code blocks using GitHub-compatible Markdown. Remove Notion styling, scripts, export identifiers, and navigation artifacts.

Use readable service names and folder names. Consolidate multiple pages about the same service without discarding unique knowledge. Keep genuinely different cross-service topics separate. Record the destination of each original HTML page in the conversion report, including pages whose content has been merged.

Preserve substantive source claims as source-derived study material. Comprehensive verification against current AWS documentation and new research are outside this conversion's scope. Explicitly flag contradictions that prevent an unambiguous conversion rather than silently inventing a resolution.

## Knowledge extracted from questions

Read questions, answer choices, indicated answers, explanations, and text-bearing screenshots. Compare each resulting concept with the destination service's existing knowledge before adding it.

- Omit a question-derived concept if the main text already covers it with the same relevant conditions.
- Add unique facts, limitations, exceptions, and practical scenario guidance to the appropriate service's Notes section. A specific existing subsection may receive an addition when necessary to keep a rule understandable in context.
- Preserve the conditions that make a scenario's conclusion valid; do not generalize a conditional answer into an unconditional rule.
- Do not reproduce quizzes, answer letters, or answer-choice lists in the final study notes.
- Do not treat incorrect distractors as factual knowledge. A useful misconception may be restated as an accurate caution only when the source supports the correction.
- Do not infer a correct answer from color or layout alone when the meaning is unclear. Record ambiguity in the conversion report.

The objective is complete coverage of unique knowledge, not verbatim retention of redundant questions.

## Images and transcriptions

Retain useful diagrams, architecture drawings, and screenshots that contribute visual information. Put them in an `assets/` folder adjacent to the page that uses them, with readable filenames and relative Markdown references. Preserve original image bytes where practical. Reuse an image when necessary without creating needless copies.

Transcribe meaningful image text into English Markdown near the image or incorporate it into the relevant notes. Question screenshots that only repeat fully converted text can be omitted from published assets. Retain a question image when its diagram or other visual detail is useful beyond the transcribed content.

Document a disposition for every source image: retained and linked, represented by converted knowledge, redundant, unreferenced, or unreadable. Flag unreadable material explicitly rather than guessing or claiming complete transcription.

## Links and conversion failures

Rewrite internal HTML links to the correct output README or heading, including links to pages that have been consolidated. Translate heading labels and rebuild affected anchors. Keep external links and code examples intact. Remove quiz navigation links when the associated knowledge has been integrated on the destination page.

Log unresolved links, missing source assets, ambiguous answers, and unreadable text in the conversion report with enough source context to locate the problem. Resolve issues supported by the available source before delivery. Any remaining limitation must be visible in the report and final handoff.

## Git preparation

Initialize Git locally and prepare the Markdown, retained assets, conversion report, and project documentation for version control. Ignore the source export folder, the original export ZIP, macOS metadata, and temporary conversion or OCR output. Preserve originals on disk. Do not add a license without a user-selected license. Remote creation, publication, and pushing are not part of this request.

The brainstorming design document is committed before implementation, as required by the invoked skill. Subsequent implementation must stage only intentional repository outputs.

## Validation and acceptance criteria

1. Every original HTML page is accounted for in the conversion report, including the root index and merged question pages.
2. Every source image has a recorded disposition; every retained image is linked from a relevant Markdown page.
3. Every generated local Markdown link and image reference resolves. Generated heading anchors used by links exist.
4. Authored study content and navigation are English; obvious untranslated prose is identified and corrected. Technical identifiers and source provenance are preserved when needed.
5. Representative complex pages are inspected for readable nested lists, tables, code blocks, illustrations, and Notes sections.
6. Question-derived notes are checked for duplication, preserved conditions, and exclusion of incorrect distractors.
7. Git excludes the original export, archive, temporary outputs, and macOS metadata.
8. The final handoff reports converted page and asset counts and any unresolved source limitations.

## Workflow boundary

This document records the approved conversational design. It must receive the user's written-spec review before the writing-plans skill creates an implementation plan. Conversion starts after that review and planning step.
