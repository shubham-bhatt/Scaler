---
name: notes
description: Turn rough lecture notes (pasted text, PDFs, text/docx files, or hand-drawn/whiteboard images) into detailed, well-structured notes plus a matching cheat sheet section for the DSA/LLD/HLD/SQL study vault under notes/. Cross-links related topics and keeps notes/INDEX.md up to date. Use whenever the user pastes rough notes from a lecture, shares a teacher's PDF/text file, or asks to "make notes", "add to my notes", "process this lecture", or "update the cheat sheet".
---

# Notes Writer

Converts raw input (typed rough notes, pasted teacher material, a PDF/text/docx
file path, or hand-drawn/whiteboard images) into structured entries in the
study vault at `notes/`, then wires the new or extended topic into the vault's
cross-link graph and index.

## Vault layout — read `notes/INDEX.md`, don't assume structure here

**`notes/INDEX.md` is the single source of truth for the vault's structure.**
It documents the subjects (buckets), the Bucket → Subbucket → Topic model, the
templates in `_templates/`, each subject's consolidated `cheatsheet.md`, and —
in each subject's **Topic Details** block — enough per-topic keyword detail to
route new content without opening every candidate file first.

**Read `INDEX.md` in full at the start of every run**, before touching
anything else. Do not hardcode the directory layout, subject list, or
file-naming convention here — if this skill's understanding of the vault ever
disagrees with `INDEX.md`, `INDEX.md` wins; fix drift by updating `INDEX.md`,
not by re-adding structure to this file.

## Workflow

1. **Get the input.**
   - Pasted rough notes → use that text directly.
   - A file path (PDF, .txt, .docx) → read it with the Read tool (Read
     supports PDF directly) or the relevant Office skill for `.docx`.
   - Referenced images (screenshots, whiteboard photos) → open them with Read
     (it's multimodal). Rough notes often leave a doubt or a step unresolved
     with "see attached image" — the image is frequently where the actual
     missing content is; don't skip it.
   - If none of the above is clear, ask what to process.
   - Don't read data from other folders than 'notes/' until user mentioned the location of the file. If the user didn't mention the location of the file, ask for it.

2. **Read `notes/INDEX.md`, then classify.**
   - For each distinct piece of content in the input, decide: subject (bucket)
     → subbucket file → topic (`##` section), using the status table and
     Topic Details blocks in `INDEX.md`.
   - **Prefer extending an existing written topic** (bold in the table, listed
     in Topic Details) over creating a new one. Only open the actual subbucket
     `.md` file once you've decided which one to edit — you shouldn't need to
     open more than one or two candidate files just to figure out *where*
     something goes.
   - If a topic doesn't exist yet, add a new `##` section to the most relevant
     existing subbucket file. Only create a brand-new subbucket file (a new
     topic family that fits none of the existing ones) by copying
     `notes/_templates/subbucket-note.md`; only create a new subject/bucket if
     it's genuinely outside DSA/LLD/HLD/SQL.
   - One input often covers several unrelated topics (e.g. a lecture that
     mixes a language gotcha with an algorithm problem) — work out the full
     topic → subbucket mapping before writing anything.

3. **If the input was a file**, copy the original into
   `notes/<Subject>/sources/<original-filename>` (unmodified) so there's a
   citable source of truth, and reference it from the frontmatter `source:`
   field of whichever subbucket file you write into. Skip this step for
   pasted-text input. For images whose content gets fully captured into the
   written note (e.g. a dry-run trace or a problem's code), they don't need to
   be kept afterward — ask the user if unsure whether to remove them.

4. **Write or extend the topic** inside its subbucket file, following
   `notes/_templates/subbucket-note.md`'s per-topic skeleton:

   ```
   ## <Topic Name>

   > One-line summary of this topic.

   ### Core Concept
   ### Details / Walkthrough
   ### Examples
   ### Common Mistakes / Edge Cases
   ### Interview Angle
   **How interviewers test this:** ...
   **Pattern Recognition — Keywords → Approach**
   | Constraint / Keyword in Problem | Think of This Pattern |
   |---|---|
   ```

   - Language for code/snippets: Java for DSA and LLD (Spring Boot idioms
     where relevant for LLD); React or Vue.js for frontend-related LLD/HLD
     topics; real SQL for SQL topics. Never pseudocode — always runnable code.
   - Rewrite/expand the rough notes into something genuinely detailed and
     correct — don't just reformat what the user typed. Fill gaps, add
     missing context, and correct errors if the rough notes got something
     wrong (flag the correction briefly, inline, don't silently overwrite it).
   - If a topic was `_Not yet written._`, replace that line with the content.
   - The "Interview Angle" section (with the keyword→pattern table) is
     required for DSA topics and recommended for LLD/HLD/SQL — for LLD/HLD,
     focus the table on "requirement phrase → design component/pattern"
     (e.g. "real-time updates" → WebSocket/SSE, "millions of users" →
     horizontal scaling/sharding).
   - Bump the subbucket file's frontmatter `updated` date, and its `status`
     (`stub` → `partial` → `complete`) if this changes how much of the file is
     written.

5. **Extend the subject's consolidated `notes/<Subject>/cheatsheet.md`** — add
   or extend the matching `###` topic section. Keep it genuinely compressed
   (one-liners, formulas, one minimal snippet) — this is for 5-minute
   pre-interview scanning, not a second copy of the full note. Bump its
   `reviewed` date.

6. **Cross-link related topics (auto-link, not just suggest).**
   - Search the vault (`Grep`/`Glob` across `notes/**/*.md`, and the Topic
     Details blocks in `INDEX.md`) for topics conceptually related to the
     new/extended one — including across subjects (e.g. LLD Observer ↔ HLD
     pub-sub, DSA heaps ↔ HLD priority scheduling).
   - Add entries to the subbucket file's `related:` frontmatter and its
     `## Related Notes` section.
   - Also **edit the related existing subbucket files** to add a reciprocal
     backlink in their own `related:` and `## Related Notes`, so links are
     bidirectional. Don't touch anything else in those files.
   - Use relative Markdown links (`[Title](../Subject/subbucket-slug.md)`),
     never bare filenames or wiki-links.

7. **Update `notes/INDEX.md`.**
   - Bold any topic name in the status table that's now written; flip the
     subbucket's status (⚪→🟡→🟢) if it changed.
   - Add or update that subbucket's line in the subject's **Topic Details**
     block with a short keyword summary of what's now covered. This is what
     keeps future runs of this skill cheap — routing new content should be
     possible from `INDEX.md` alone, without reopening every subbucket file.

8. **Report back concisely**: which subbucket files were created vs.
   extended, which topics changed status, and which existing notes got a new
   cross-link — so the user can see the graph growing without opening every file.

## Notes on quality

- Detailed note = teach it properly, as if writing the explanation you'd want
  when revising months later. Cheat sheet = compressed for 5-minute
  pre-interview scanning. Keep them genuinely different in density, not the
  same content twice.
- Prefer merging into an existing topic over creating near-duplicates — check
  `INDEX.md`'s Topic Details block before writing.
- Keep filenames and headings stable once created; append/extend rather than
  renaming, so links made elsewhere in the vault don't break.
- Set `difficulty` and `frequency` based on the content and your knowledge of
  interview patterns; infer difficulty if the rough notes don't hint at it.
  `frequency` reflects how commonly the topic appears in real interviews.
- Set `reviewed` to the creation date on first write. The user updates this
  field manually when they revisit the note; don't auto-bump it on edits.
- If a referenced source (external PDF, image) doesn't fully resolve — e.g. a
  problem statement mentioned but not captured anywhere — write the note from
  what's available, flag clearly what was reconstructed vs. sourced verbatim,
  and note what's still missing, rather than leaving a silent gap.
