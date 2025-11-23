# AI Agent Commands

These are AI agent commands that I use in AI-powered IDE's/tools such Cursor, Windsurf, Claude Code, and Copilot Agent Mode.

## What Are AI Agent Commands?

By "commands", I mean a shortcut to a re-usable prompt. Most IDE's / AI tools have some way to accomplish them.

Here's a breakdown of what these are called in each IDE:

| IDE | Name |
| --- | --- |
| Claude | [Commands](https://code.claude.com/docs/en/slash-commands) |
| Copilot Agent Mode | [Prompt Files](https://code.visualstudio.com/docs/copilot/customization/prompt-files) |
| Cursor | [Commands](https://cursor.com/docs/agent/chat/commands) |
| Windsurf | [Workflows](https://docs.windsurf.com/windsurf/cascade/workflows) |

## My Commands

### `/codereview` [🔗](./commands/codereview.md)

> ⚠️ Warning: This is slow for large PR's because it runs `git diff` for every single file. However, I've noticed it also tends to be more accurate (Probably because it is only looking at one changed file at a time). For a faster version, see `/codereview-fast`.

Have the agent perform a code review on your changes compared against the main branch. Adds comments prefixed with `!AI_GENERATED!`. Good for reviewing your own code or others. Great in combination with `/address-ai-comments` for reviewing your own code.

<details>
<summary>Expand to see command</summary

```md
Your task is to act as an automated code reviewer. You will review each changed file in this branch one by one adding comments directly to the code for any issues you find.

# Instructions

1. Find the common ancestor commit. Run git merge-base origin/main HEAD and refer to the output as MERGE_BASE.

2. Get the list of changed files. Run git --no-pager diff --name-only MERGE_BASE..HEAD.

3. Review files sequentially. For each file in the list you just generated, perform the following steps. You must complete the entire review for one file before starting the next.

3a. Announce the file. Start by stating: "Reviewing: [filename]".

3b. Get the file's changes. Run git --no-pager diff MERGE_BASE..HEAD <filename> to see the specific diff for the current file.

3c. Analyze each change. For each modified chunk of code in the file:
* Examine the changed lines in their original context using the view_line_range tool.
* If the code is fine, state: "Looks good. Moving on...".
* If you find an issue, proceed to the next step immediately.

3d. Add review comments directly to the file. Use the replace_file_content tool to add your feedback as comments in the code.
* Format: Your comments must follow this format:
// !AI_GENERATED! COMMENT_TYPE - Your concise description of the issue.
* Placement: Place the comment on the line immediately before the code you are referencing.
* Efficiency: If a file requires multiple comments, group them all into a single replace_file_content call for that file.

3e. Use the correct comment type.
* 🐛 BUG - A potential bug or logic error.
* 🔒 SECURITY - A security vulnerability.
* ⚡️ PERFORMANCE - A performance issue or optimization.
* 📝 STYLE - A code style, readability, or maintainability issue.
* 💭 SUGGESTION - A general improvement.
* ⚠️ WARNING - A potential edge case or risky code.
* ❓ QUESTION - A request for clarification.

4. Repeat. Once you have finished reviewing a file and added all necessary comments, move on to the next file in the list and repeat step 3.
```
</details>

### `/codereview-fast` [🔗](./commands/codereview-fast.md)

This is practically the same as `/codereview`, but it's a little faster (although probably less accurate) because it only runs `git diff` once and then processes that all at once.

<details>
<summary>Expand to see command</summary>

```md
Your task is to act as an automated code reviewer. You will review each changed file in this branch one by one adding comments directly to the code for any issues you find.

# Instructions

1. Find the common ancestor commit. Run git merge-base origin/main HEAD and refer to the output as MERGE_BASE.

2. Get the list of changed files. Run git --no-pager diff --name-only MERGE_BASE..HEAD.

3. Review files sequentially. For each file in the list you just generated, perform the following steps. You must complete the entire review for one file before starting the next.

3a. Announce the file. Start by stating: "Reviewing: [filename]".

3b. Get the file's changes. Run git --no-pager diff MERGE_BASE..HEAD <filename> to see the specific diff for the current file.

3c. Analyze each change. For each modified chunk of code in the file:
* Examine the changed lines in their original context using the view_line_range tool.
* If the code is fine, state: "Looks good. Moving on...".
* If you find an issue, proceed to the next step immediately.

3d. Add review comments directly to the file. Use the replace_file_content tool to add your feedback as comments in the code.
* Format: Your comments must follow this format:
// !AI_GENERATED! COMMENT_TYPE - Your concise description of the issue.
* Placement: Place the comment on the line immediately before the code you are referencing.
* Efficiency: If a file requires multiple comments, group them all into a single replace_file_content call for that file.

3e. Use the correct comment type.
* 🐛 BUG - A potential bug or logic error.
* 🔒 SECURITY - A security vulnerability.
* ⚡️ PERFORMANCE - A performance issue or optimization.
* 📝 STYLE - A code style, readability, or maintainability issue.
* 💭 SUGGESTION - A general improvement.
* ⚠️ WARNING - A potential edge case or risky code.
* ❓ QUESTION - A request for clarification.

4. Repeat. Once you have finished reviewing a file and added all necessary comments, move on to the next file in the list and repeat step 3.
```
</details>

### `address-ai-comments` [🔗](./commands/address-ai-comments.md)

Have the agent address all of the code review comments generated by `/codereview` (or `/codereview-fast`).

<details>
<summary>Expand to see command</summary>

```md
Address AI Comments

1. Find all comments by searching for all instances of comments starting with "!AI_GENERATED!".
2. For each comment:
  a. Make the necessary changes to the code to address the comment
  b. Remove the "!AI_GENERATED!" comment once addressed
```
</details>

### `/address-pr-comments`

Agent uses the GitHub CLI to get all code reviewer comments on a PR. Addresses all the ones that do not have the eyes reaction emoji (👀).

<details>

```md
1. Ask for url to PR
2. Determine the following variables from the url:
    - repo_owner
    - repo_name
    - pr_id
3. Get comments on PR

 bash
  gh api --paginate -H "Accept: application/vnd.github+json" -H "X-GitHub-Api-Version: 2022-11-28" [repo_owner]/[repo_name]/pulls/[pr_id]/comments | jq '.[] | {user: .user.login, body, path, line, original_line, created_at, in_reply_to_id, pull_request_review_id, commit_id, seen: .reactions.eyes} | select(.seen == 0)'

4. For EACH comment, do the following. Remember to address one comment at a time.
 a. Print out the following: "(index). From [user] on [file]:[lines] — [body]"
 b. Analyze the file and the line range.
 c. If you don't understand the comment, do not make a change. Just ask me for clarification, or to implement it myself.
 d. If you think you can make the change, make the change BEFORE moving onto the next comment.

5. After all comments are processed, summarize what you did, and which comments need the USER's attention.
```
</details>
