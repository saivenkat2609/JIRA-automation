# Project Context

This document provides high-level context about this project to help AI models (and humans) understand the **why** behind implementation decisions.

## Project Purpose

**What**: Automated JIRA release notes generation from GitHub pull requests
**Why**: Manual release note writing is time-consuming, inconsistent, and often incomplete
**Goal**: Automatically generate detailed, contextualized release notes when PRs are merged

## Problem Statement

### Before This Project
- Developers merged PRs but forgot to update JIRA tickets
- Release notes were inconsistent in quality and format
- Product managers had to manually track down what changed
- Historical context of changes was lost or scattered

### After This Project
- PRs merged → Release notes automatically generated via AI
- JIRA tickets automatically updated with detailed notes
- Consistent format: WHY, WHAT, HOW, Technical details, Impact
- Historical record maintained in JIRA

## Key Design Decisions

### 1. Why GitHub Actions?
**Decision**: Run automation as GitHub Actions workflow
**Why**:
- Runs automatically on PR merge
- No separate infrastructure to maintain
- Native access to GitHub PR data
- Free for public repos

**Alternatives Considered**:
- Jenkins: Too heavy, requires dedicated server
- Webhooks + Lambda: More complex setup
- Manual process: Unreliable human compliance

### 2. Why OpenAI for Note Generation?
**Decision**: Use OpenAI GPT-4o-mini to generate release notes
**Why**:
- Understands code context and can explain "why"
- Generates consistent, readable prose
- Cost-effective (gpt-4o-mini is cheap)
- Better than templates (which miss context)

**Alternatives Considered**:
- Templates: Too rigid, don't explain reasoning
- Manual: Inconsistent, often skipped
- Rule-based: Can't understand intent

### 3. Why Extract from PR Title/Body (not diff)?
**Decision**: Changed from parsing diff to using PR title and description
**Why**:
- PR descriptions already contain human context
- Diffs are too noisy and miss the "why"
- Character limits make full diffs impractical
- Human-written context > AI interpreting code

**Evolution**: Started with diff parsing, switched to PR metadata for better quality

### 4. Why Branch Name Parsing?
**Decision**: Extract JIRA issue key (e.g., PROJ-123) from branch name
**Why**:
- Standard git workflow: create branch from JIRA ticket
- Automatic linking without manual configuration
- Enforces naming convention
- Zero extra effort for developers

## User Workflow

1. Developer creates branch from JIRA ticket (e.g., `PROJ-123-fix-login-bug`)
2. Developer makes changes and creates PR with descriptive title/body
3. PR gets reviewed and merged
4. **GitHub Action triggers automatically**:
   - Extracts JIRA issue key from branch name
   - Reads PR title and description
   - Sends to OpenAI to generate release notes
   - Posts notes as comment on JIRA ticket
5. Product team sees detailed release notes in JIRA

## Technical Architecture

```
GitHub PR Merged
    ↓
GitHub Actions Workflow
    ↓
Extract JIRA Issue Key (from branch)
    ↓
Fetch PR Title & Body
    ↓
Send to OpenAI API
    ↓
Generate Release Notes
    ↓
Post to JIRA via REST API
```

## Success Metrics

- ✅ Every merged PR automatically updates JIRA
- ✅ Release notes include WHY, WHAT, HOW, Impact
- ✅ Consistent format across all tickets
- ✅ Zero manual developer effort

## Future Considerations

### Potential Improvements
- Support multiple JIRA issues per PR
- Add validation that PR description is filled out
- Generate notes in multiple formats (Jira, Slack, Confluence)
- Aggregate notes across multiple PRs for release summaries

### Known Limitations
- Requires JIRA issue key in branch name
- Depends on quality of PR descriptions
- OpenAI API costs (though minimal with gpt-4o-mini)
- Requires secrets configuration in GitHub

## Related Documentation

- `CHANGELOG.md` - Historical record of project changes
- `.github/PULL_REQUEST_TEMPLATE.md` - Ensures quality PR descriptions
- `docs/adr/` - Architecture decision records for major technical choices
- `.github/workflows/jira-release-notes.yml` - Implementation details

## Questions This Answers

**Q: Why do we need AI for this?**
A: Templates can't explain context. AI reads PR descriptions and explains WHY changes were made, not just WHAT changed.

**Q: Why not use JIRA's built-in automation?**
A: JIRA automation can't generate contextual prose from GitHub PR data. It's rule-based, not AI-powered.

**Q: Why require JIRA key in branch name?**
A: Automatic linking without manual input. Enforces good git hygiene.

**Q: Why OpenAI instead of open-source models?**
A: Cost-effective, reliable, good output quality. Can switch to alternatives if needed (see ADR docs for migration path).
