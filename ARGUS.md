# Odysseus Improvement Checklist

## 1. MCPs

- [ ] List all current MCPs in the registry
- [ ] Verify each MCP is running
- [ ] Document any **Failed** states and plan repair steps
- [ ] Set up alerts for future failures

## 2. Obsidian Integration

- [ ] Confirm Obsidian vault path is configured in `config/obsidian.yml`
- [ ] Enable automatic two‑way sync between Obsidian and Odysseus
- [ ] Create a sample vault folder structure for quickstart
- [ ] Write sample macro to import a daily note into Odysseus brain

## 3. Chat Enhancements

- [ ] Add persona prompt templates:
  - `dev_bot`
  - `researcher`
  - `assistant`
- [ ] Slice persona selection into a reusable chat widget
- [ ] Store best‑practice prompts in `_knowledgebase/chat-personas.md`
- [ ] Add UI toggle for “Smart reply” feature

## 4. Deep Research & Reports

- [ ] Build a template for comprehensive research reports
- [ ] Hook research outputs to the comparison tool
- [ ] Automate periodic research on “AI & Ethics” topic
- [ ] Export reports to PDF and store in Obsidian

## 5. Email & Calendar Automation

- [ ] Create a mailbox parser rule for auto‑classification
- [ ] Schedule daily email digest via `manage_tasks`
- [ ] Link calendar reminders to `manage_notes` checklist items
- [ ] Add a “Meeting follow‑up” note template

## 6. Brain / Memories

- [ ] Log key decisions in a `memos/` folder
- [ ] Create a “Lessons Learned” knowledge base
- [ ] Set up a neural‑network‑driven suggestion for next steps

## 7. Comparison Tools

- [ ] Integrate `compare` view into the dashboard
- [ ] Write unit tests for comparison algorithm
- [ ] Add a visual diff in Obsidian

## 8. Cookbook Extensions

- [ ] Add a new preset for `qwen3-8b` with 4‑bit weights
- [ ] Create a walkthrough for running the new preset
- [ ] Publish the preset to the shared Cookbook repo

## 9. Gallery & Library

- [ ] Import recent screenshots into the gallery
- [ ] Tag gallery items with tags: `design`, `documentation`
- [ ] Link library artifacts to related Odysseus tasks

## 10. Notes & Tasks

- [ ] Use `ARGUS.md` as a baseline checklist but utilize Tasks and Notes too!
- [ ] Automate task creation from email requests
- [ ] Sync unfinished tasks to Obsidian daily

## 11. Themes & UI

- [ ] Build a dark‑mode theme from scratch or modify a clone of an existing one.
- [ ] Adapt color palette to the brand guidelines
- [ ] Add an accessibility layer (high‑contrast, font size options)

---

*Use this document as the single source of truth for Odysseus improvement.  Check items off as they are completed and revisit monthly.*
