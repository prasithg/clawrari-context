# Feature Pipeline — Where Clawrari Features Come From

## The 5 Sources

Unlike a product company where features come from customer research, Clawrari features come from daily operations. There are 5 natural sources:

### 1. Things We Build For Ourselves
We build a new skill, write a cron, improve memory architecture, fix a regression — then ask: "Should this be in Clawrari?" Most features originate here.

**Example:** `mac-screenshots` skill was born from a 3x regression of fumbling macOS screenshot filenames. Built the skill to fix it. Realized it's universally useful. Added to Clawrari.

### 2. Things That Break
Every regression, every failed cron, every fumbled workflow is a potential Clawrari improvement. The regressions pipeline IS the feature backlog.

**Example:** QMD memory index went stale for 19 days with no alert. Added QMD freshness guard to heartbeat. That pattern becomes a Clawrari health check.

### 3. Things We Learn
Research, community scans, Reddit finds, new techniques, patterns from other builders. When we learn something useful, it becomes a skill or reference doc.

**Example:** Willison's agentic engineering patterns → extracted to reference doc → became part of Clawrari's coding standards.

### 4. Things People Ask For
GitHub issues, Discord requests, community PRs. This source activates after the repo is public and has users.

### 5. Content-Driven Discovery
Writing a post reveals a gap. "I wanted to write about our RLHF loop but realized it's not packaged as a reusable pattern." The content need drives the feature.

**Example:** Planning a thread about "how my AI gets better every day" → realized the self-improvement loops aren't documented as a framework → FRD-C3 born.

---

## The Daily Flow

```
Morning: Run Clawrari (work + personal) normally
    ↓
During day: Notice improvements, fixes, new patterns
    ↓
End of day: Session brief captures what changed
    ↓
Weekly: Review session briefs → extract Clawrari candidates
    ↓
Candidate passes filter? → Write discovery brief
    ↓
Discovery brief validated? → FRD → Blueprint → Work Orders → Build
```

---

## The Extraction Filter

Before something becomes a Clawrari feature, it must pass all four:

1. **Is it generalizable?** Would someone who isn't Prasith benefit from this?
2. **Is it stable?** Have we used it for at least a week without major changes?
3. **Is it documentable?** Can we explain the "why" not just the "how"?
4. **Is there a content angle?** Can we write about this?

If all four are yes → write the discovery brief.
If not → stays in personal workspace.

---

## The Packaging Pipeline

```
Personal workspace (our live setup)
    ↓ extract + generalize
Clawrari repo (~/Development/clawrari)
    ↓ FRD + blueprint + work orders
Coding agent builds it
    ↓ review + test
PR merged → version bumped
    ↓ content
Post about it (Twitter/LinkedIn)
```

### Extraction Rules
- **Never copy private data.** API keys, personal contacts, company specifics stay in personal workspace.
- **Abstract aggressively.** Replace Prasith-specific values with template variables or configuration prompts.
- **Include examples.** Ship both the template AND a filled-in example (using sanitized data).
- **Test the bootstrap.** Every feature must work in a fresh Clawrari install, not just our existing setup.

---

## Versioning

Clawrari follows semver:
- **Major:** Breaking changes to base layer or persona architecture
- **Minor:** New features, new skills, new patterns
- **Patch:** Fixes, documentation, small improvements

Every version bump gets a changelog entry and (ideally) a content post.
