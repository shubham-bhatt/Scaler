---
name: notes
description: Turn rough lecture notes (pasted text, PDFs, or text/docx files) into a detailed, well-structured note plus a companion cheat sheet for the DSA/LLD/HLD study vault under notes/. Cross-links related topics and keeps notes/INDEX.md up to date. Use whenever the user pastes rough notes from a lecture, shares a teacher's PDF/text file, or asks to "make notes", "add to my notes", "process this lecture", or "update the cheat sheet".
---

# Notes Writer

Converts raw input (typed rough notes, pasted teacher material, or a PDF/text/docx
file path) into two polished Markdown files inside the study vault at `notes/`,
then wires the new topic into the vault's cross-link graph and index.

The vault covers three subjects: **DSA**, **LLD**, **HLD** — for interview prep
(coding + design rounds). Everything is plain Markdown so it renders correctly
on GitHub or any plain viewer (no Obsidian-only syntax).

## Vault layout

```
notes/
  INDEX.md                          # master index, one section per subject
  DSA/
    <topic-slug>.md                 # detailed note
    cheatsheets/<topic-slug>-cheatsheet.md
    sources/                        # original PDFs/files copied here for reference
  LLD/  (same layout)
  HLD/  (same layout)
```

## Workflow

1. **Get the input.**
   - If the user pasted rough notes directly in chat, use that text.
   - If they gave a file path (PDF, .txt, .docx), read it with the Read tool
     (Read supports PDF directly) or the relevant Office skill for .docx.
   - If neither is clear, ask what to process.

2. **Classify subject + topic.**
   - Decide DSA / LLD / HLD from content. If genuinely ambiguous, ask.
   - Pick a short kebab-case topic slug (e.g. `sliding-window`,
     `observer-pattern`, `rate-limiter-design`).
   - Glob `notes/<Subject>/*.md` first. If a file for this topic (or a clear
     near-duplicate) already exists, **update/merge into it** rather than
     creating a second file for the same concept — append new material under
     the right heading, don't duplicate sections.

3. **If the input was a file**, copy the original into
   `notes/<Subject>/sources/<original-filename>` (unmodified) so there's a
   citable source of truth, and reference it from the note's frontmatter
   (`source:` field). Skip this step for pasted-text input.

4. **Write the detailed note** at `notes/<Subject>/<topic-slug>.md`:

   ```markdown
   ---
   title: <Human Readable Title>
   subject: DSA | LLD | HLD
   tags: [tag1, tag2]
   difficulty: easy | medium | hard
   frequency: low | medium | high
   created: <YYYY-MM-DD>
   updated: <YYYY-MM-DD>
   reviewed: <YYYY-MM-DD>
   source: <path under sources/, or "lecture notes">
   related: [<other-topic-slug>, ...]
   ---

   # <Title>

   > One-paragraph plain-English summary — what this is and why it matters
   > for interviews.

   ## Core Concept
   (explain properly, don't just restate the rough notes — fill gaps,
   define terms, add the "why", not just the "what")

   ## Details / Walkthrough
   (sub-headed sections as needed: mechanism, steps, variants, complexity
   analysis for DSA; class design, trade-offs for LLD; components,
   trade-offs, scaling numbers for HLD)

   ## Examples
   (worked example, or a small code snippet, matched to the subject)

   ## Common Mistakes / Edge Cases

   ## Interview Angle
   ### How interviewers test this
   (what variations to expect, typical follow-up questions, how the
   problem is escalated mid-interview — e.g. "what if the input
   doesn't fit in memory?", "can you do it in-place?")

   ### Pattern Recognition — Keywords → Approach
   | Constraint / Keyword in Problem | Think of This Pattern |
   |---|---|
   | "contiguous subarray", "window of size k" | Sliding Window |
   | ... | ... |
   (Map the specific constraints, phrases, and keywords that appear
   in interview problem statements to the solving pattern taught in
   this topic. This table helps the reader build the reflex:
   "when I see X in a problem, I should reach for Y.")

   ## Related Notes
   - [<Other Topic>](../<OtherSubject>/<other-topic-slug>.md) — why it's related

   ## Cheat Sheet
   → [<Title> Cheat Sheet](cheatsheets/<topic-slug>-cheatsheet.md)
   ```

   Rewrite/expand the rough notes into something genuinely detailed and
   correct — don't just reformat what the user typed. Fix gaps, add missing
   context, correct errors if the rough notes got something wrong (flag the
   correction inline briefly).

5. **Write the cheat sheet** at
   `notes/<Subject>/cheatsheets/<topic-slug>-cheatsheet.md`. Keep it short
   and scannable — this is for last-minute review, not re-reading the full
   note. Always include all three sections:

   ```markdown
   ---
   title: <Title> — Cheat Sheet
   subject: DSA | LLD | HLD
   related_note: ../<topic-slug>.md
   reviewed: <YYYY-MM-DD>
   ---

   # <Title> — Cheat Sheet

   ## Quick Recall
   - key definitions, formulas, time/space complexity, one-liners

   ## Patterns / Snippets
   - reusable code template or design pattern skeleton
     **Language**: Java for DSA and LLD (use Spring Boot idioms for
     LLD where relevant); React or Vue.js for frontend-related LLD/HLD
     topics. Never use pseudocode — always real, runnable code.

   ## Pitfalls & Likely Questions
   - Q: ... A: ... (terse)
   - gotchas / edge cases that trip people up
   ```

6. **Cross-link related topics (auto-link, not just suggest).**
   - Search existing notes (`Grep`/`Glob` across `notes/**/*.md`) for topics
     conceptually related to the new one — including across subjects (e.g.
     LLD Observer pattern ↔ HLD pub-sub, DSA heaps ↔ HLD priority
     scheduling).
   - Add entries to the new note's `related:` frontmatter and `## Related
     Notes` section.
   - Also **edit the related existing notes** to add a backlink to the new
     note in their own `## Related Notes` section and `related:`
     frontmatter, so links are bidirectional. Don't touch anything else in
     those files.
   - Use relative Markdown links (`[Title](../Subject/topic-slug.md)`),
     never bare filenames or wiki-links.

7. **Update `notes/INDEX.md`.**
   - One `##` section per subject (DSA, LLD, HLD), each a bullet list:
     `- [Title](DSA/topic-slug.md) · [cheat sheet](DSA/cheatsheets/topic-slug-cheatsheet.md)`.
   - Add the new topic under the right subject, keeping the list
     alphabetical. If the file doesn't exist yet, create it with all three
     subject headings.

8. **Report back concisely**: which files were created vs. updated, and
   which existing notes got a new cross-link — so the user can see the
   graph growing without opening every file.

## Notes on quality

- Detailed note = teach it properly, as if writing the explanation you'd
  want when revising months later. Cheat sheet = compressed for 5-minute
  pre-interview scanning. Keep them genuinely different in density, not
  the same content twice.
- Prefer merging into existing topic files over creating near-duplicates —
  check the vault before writing.
- Keep filenames and headings stable once created; append/extend rather
  than renaming, so links made elsewhere in the vault don't break.
- Set `difficulty` and `frequency` based on the content and your
  knowledge of interview patterns. If the rough notes don't hint at
  difficulty, infer from the concept's complexity. `frequency` reflects
  how commonly the topic appears in real tech interviews.
- Set `reviewed` to the creation date on first write. The user will
  update this field manually when they revisit the note; don't
  auto-bump it on edits.
- The "Interview Angle" section is required for DSA notes and
  recommended for LLD/HLD. The keyword→pattern table is especially
  important for DSA — it trains pattern-matching instincts. For LLD/HLD,
  focus the table on "requirement phrases → design component/pattern"
  (e.g. "real-time updates" → WebSocket / SSE, "millions of users" →
  horizontal scaling / sharding).
