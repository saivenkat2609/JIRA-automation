# Contributing Guide

This guide explains **how** and **why** we follow certain practices in this project.

## Branch Naming Convention

### Format
```
<JIRA-KEY>-<brief-description>
```

### Examples
✅ Good:
- `PROJ-123-fix-login-redirect`
- `TEAM-456-add-user-avatar`
- `BUG-789-handle-null-pointer`

❌ Bad:
- `fix-bug` (no JIRA key)
- `PROJ-123` (no description)
- `my-feature-branch` (no JIRA key)

### Why This Matters
- The automation workflow extracts the JIRA key from your branch name
- Without it, the workflow will fail and release notes won't be generated
- It creates automatic traceability between code and tickets

## Pull Request Guidelines

### PR Title
Write a clear, concise title that describes WHAT changed:
```
Add user authentication with JWT tokens
Fix infinite loop in data processing
Update dependencies to latest versions
```

### PR Description (CRITICAL)
The AI uses your PR description to generate release notes, so be thorough:

#### Required Sections

**1. Why are we making this change?**
Explain the problem, bug, or feature request that prompted this work.

Example:
```
Users were unable to log in after their session expired because the
app didn't handle token refresh. This caused frustration and support tickets.
```

**2. What was implemented?**
Describe the solution at a high level.

Example:
```
Added automatic token refresh logic that detects expired tokens and
silently refreshes them before API calls. Falls back to login page
if refresh fails.
```

**3. How does it work?**
Explain the approach or technical details.

Example:
```
Implemented an axios interceptor that checks token expiry before each
request. If expired, calls /refresh endpoint to get new token, then
retries the original request.
```

**4. What's the impact?**
Describe how this affects users or the system.

Example:
```
- Users stay logged in longer without interruption
- Reduced support tickets related to authentication
- Added 2 new API endpoints (/refresh, /logout)
- Slight increase in API calls due to refresh checks
```

### Why PR Descriptions Are Important
- **The AI reads your PR description to generate release notes**
- Good descriptions → Good release notes
- Empty descriptions → Generic/useless release notes
- This is your chance to document the "why" for the future

## Commit Messages

### Format
Follow conventional commits:
```
<type>(<scope>): <subject>

<body>

<footer>
```

### Examples
```
feat(auth): add JWT token refresh mechanism

Implemented automatic token refresh to improve user experience.
Users no longer need to re-login when tokens expire.

Closes PROJ-123
```

```
fix(api): handle null response from user endpoint

Added null checks to prevent crashes when user data is missing.
This was causing 500 errors in production.

Fixes BUG-456
```

### Why Good Commits Matter
While the workflow focuses on PR-level notes, good commit messages:
- Help during code review
- Make git history readable
- Assist with debugging and git bisect
- Show your thought process

## Testing Your Changes

### Before Creating a PR

1. **Test locally**
   - Run the application
   - Verify your changes work as expected
   - Check for console errors

2. **Review your own code**
   - Read through your diff
   - Remove debug code, console.logs
   - Check for security issues

3. **Write tests if applicable**
   - Unit tests for new functions
   - Integration tests for new features
   - Update existing tests if behavior changed

### Why This Matters
- Broken PRs block other developers
- Poor quality PRs waste reviewer time
- The release notes will reflect whatever you merge

## Code Review Expectations

### As a Reviewer
- Focus on correctness, security, maintainability
- Ask questions if intent isn't clear
- Suggest improvements, don't demand perfection
- Approve if the code is better than before

### As an Author
- Respond to all comments
- Don't take feedback personally
- Explain your reasoning if you disagree
- Update the PR based on feedback

### Why Code Review Matters
- Catches bugs before they reach production
- Shares knowledge across team
- Ensures consistent code quality
- Multiple perspectives improve solutions

## Secrets Management

### Required GitHub Secrets
- `OPENAI_API_KEY` - For generating release notes
- `JIRA_EMAIL` - Your JIRA account email
- `JIRA_API_TOKEN` - JIRA API token (not password)
- `JIRA_BASE_URL` - Your JIRA instance URL

### Security Best Practices
- ✅ Use GitHub Secrets (encrypted at rest)
- ✅ Rotate tokens periodically
- ✅ Use minimal permissions (read/write issues only)
- ❌ Never commit secrets to code
- ❌ Never log secrets in GitHub Actions output

### Why Secrets Matter
- Compromised tokens = unauthorized access to JIRA
- Logged secrets = security breach
- GitHub Secrets are the secure way to handle this

## Workflow Debugging

### If Release Notes Aren't Generated

1. **Check branch name** - Does it contain JIRA key?
2. **Check workflow logs** - Go to Actions tab in GitHub
3. **Check PR description** - Is it filled out?
4. **Check secrets** - Are all required secrets configured?
5. **Check JIRA permissions** - Can the token post comments?

### Why Debugging Is Important
Failed workflows = No automation benefit
Understanding the workflow helps you fix issues quickly

## Questions?

If something isn't clear:
1. Check `CONTEXT.md` for high-level project understanding
2. Check `docs/adr/` for architecture decisions
3. Open an issue to discuss improvements
4. Update this guide if you found a gap

## Remember

**The goal of these practices is to:**
- Make automation work reliably
- Create high-quality release notes automatically
- Maintain clear history of changes
- Reduce manual work for everyone

Following these guidelines benefits the whole team!
