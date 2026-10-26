# Cheat Sheet Markdown Implementation Plan

> **For agentic workers:** Use superpowers:subagent-driven-development or superpowers:executing-plans to implement this plan task by task. Steps use checkbox syntax for tracking.

**Goal:** Deliver an English GitHub-ready AWS study repository with complete source accounting, useful illustrations, and deduplicated question-derived Notes.

**Architecture:** Inventory the source before converting it. Use temporary extraction, translation, and OCR artifacts to produce service READMEs, then perform editorial consolidation and source-to-output checks. Publish only Markdown, retained illustrations, provenance, and project documentation.

**Tech Stack:** Python, Pandoc, Tesseract, Markdown, Git. Temporary Python dependencies live in an ignored virtual environment.

---

## File responsibilities

- `README.md`: English introduction, service index, scope, and repository usage.
- `.gitignore`: exclude original source exports, ZIP archives, macOS metadata, and temporary tooling.
- `services/<service>/README.md`: translated main knowledge and unique Notes.
- `services/<service>/<topic>/README.md`: meaningful nested topics, excluding quizzes.
- `services/<service>/assets/*`: useful original illustrations linked from the relevant README.
- `docs/conversion-report.md`: coverage totals, page destinations, image dispositions, and explicit source limitations.
- `.work/`: disposable manifests, OCR text, translation cache, temporary scripts, and review artifacts; excluded from Git.

This is a document conversion. Validate content coverage and rendered Markdown structure rather than adding application tests or a runtime dependency to the published repository.

## Task 1: Prepare the conversion workspace

- [ ] Create a working branch in the existing new repository so outputs remain in the folder the user selected.
- [ ] Create `.gitignore` with `/Private & Shared/`, `/*.zip`, `.DS_Store`, `.work/`, `__pycache__/`, and `.venv/`.
- [ ] Create an ignored `.work/` directory and a temporary Python environment if required.
- [ ] Inventory every HTML page and image into `.work/manifest.json`, assigning English service names, output slugs, and explicit merge destinations for duplicate service and question pages.
- [ ] Check inventory totals against 98 HTML pages and 214 images. Ensure the original source remains unchanged.

## Task 2: Extract structured page content and image text

- [ ] Parse article bodies, excluding scripts, CSS, and export navigation. Preserve tables, lists, inline code, and fenced code.
- [ ] Convert each article to temporary Markdown using Pandoc, storing drafts in `.work/drafts/` keyed by the manifest page ID.
- [ ] Run OCR on each PNG with Tesseract; extract SVG labels directly. Store per-image text and confidence in `.work/ocr/`.
- [ ] Inspect illustrations and question screenshots when OCR or answer indications are ambiguous. Use the original images to resolve recognition errors, especially numerical limits and answer selections.
- [ ] Verify every source page and image has an extraction record, including empty and image-only pages.

## Task 3: Translate the main knowledge into English

- [ ] Translate Vietnamese prose while protecting code, URLs, image links, and technical identifiers. Store translation results in `.work/translation-cache.json` so interrupted processing can resume.
- [ ] Write each service's main translated content to its assigned README with a clear English title.
- [ ] Merge duplicate AWS Glue and Resource Access Manager pages into their respective services, keeping unique content.
- [ ] Preserve the Aurora Multi-AZ and VPC public/private subnet topics as nested folders and add parent/back links.
- [ ] Review generated headings, lists, tables, code blocks, and terminology. Remove export artifacts, conversational exam framing, and untranslated author commentary.

## Task 4: Consolidate question knowledge and image transcriptions

- [ ] For each question page and screenshot, compare its source facts with the service's translated main notes.
- [ ] Add only new facts and scenario conditions to the service's Notes section. Preserve useful calculations and practical limitations.
- [ ] Remove duplicate explanations, quiz prompts, answer letters, incorrect distractors, and answer-choice lists.
- [ ] Consolidate question explanations embedded in main service pages using the same rules.
- [ ] Retain useful diagrams with English captions and text explanations. Omit screenshots that only repeat fully transcribed question knowledge.
- [ ] Record each image disposition and any genuinely unreadable or ambiguous information. Do not infer missing answers or silently resolve contradictory source claims.

## Task 5: Build navigation and provenance

- [ ] Write the root README with an alphabetical index linking every service/topic and its substantive child topics.
- [ ] Rewrite source HTML references and heading anchors to generated destinations. Preserve external URLs and code examples.
- [ ] Write `docs/conversion-report.md` with a destination for each of the 98 source pages and a disposition for each of the 214 images.
- [ ] Explain that source claims are study notes, not a comprehensive update against current AWS documentation. List concrete conversion limitations only where they remain.
- [ ] Include basic local Git publishing commands without inventing a remote or selecting a license.

## Task 6: Validate and review

- [ ] Check that every local Markdown and image link resolves, every retained image is linked, and linked heading anchors exist.
- [ ] Check complete manifest coverage and detect untracked output files, accidental source staging, duplicate sections, Vietnamese prose, and residual quiz formatting.
- [ ] Review complex pages including EC2, S3, VPC, IAM, RDS, Route 53, and image-only topics. Check numerical facts against their source and OCR.
- [ ] Perform specification review, then editorial/structural quality review; fix actionable findings before the final audit.
- [ ] Confirm the original HTML and ZIP remain on disk and are ignored by Git.

## Task 7: Deliver the repository

- [ ] Mark this plan's completed steps and update conversion counts to match verified final outputs.
- [ ] Run `git diff --check` and the source/output audit, and inspect `git status --short`.
- [ ] Commit only intentional output files with an English commit message. Keep the result in the user's selected workspace, ready to upload.
- [ ] Report the README location, service/topic counts, image handling, validation results, and any material limitations. Do not push or create a remote.
