1. Ask for url to PR
2. Get the PR diff

 bash
  GH_PAGER=cat gh pr diff [PR_URL]

3. Parse the diff output to identify modified files and **exact lines that were changed**
   - Lines starting with '+' are ADDED in your branch (new code)
   - Lines starting with '-' are REMOVED from master (old code)
4. For EACH modified file, perform a code review following these steps:
   a. Announce the file being reviewed: "**Reviewing: [filename]**"
   b. Run a search to locate the file and use view_file_outline or view_line_range to examine the code that was changed
   c. For each chunk of modified code in the file:
      i. Show the specific lines that were modified (with line numbers)
      ii. If the section of code doesn't need improvement, announce "Looks good. Moving on..."
      iii. Otherwise, IMMEDIATELY USE THE replace_file_content TOOL to directly add comments to the file using the appropriate comment syntax for the language (e.g., `//` for JavaScript, `#` for Python).
      iv. DO NOT just describe what changes are needed in chat - actually make the edits directly to the file.

5. Add code review comments in the following format:
```
// [LINE X-Y]: !AI_GENERATED! COMMENT_TYPE - Description of the issue or suggestion
```
   Where COMMENT_TYPE is one of:
   - 🐛 BUG - Potential bug or logic error
   - 🔒 SECURITY - Security concern or vulnerability  
   - ⚡️ PERFORMANCE - Performance issue or optimization opportunity
   - 📝 STYLE - Code style, readability, or maintainability issue
   - 💭 SUGGESTION - General improvement suggestion
   - ⚠️ WARNING - Potential edge case or risky code
   - ❓ QUESTION - Clarification needed

6. IMPORTANT: When adding comments to a file:
   a. Use the replace_file_content tool
   b. Be precise with line numbers in your TargetContent
   c. Make sure to add the comment on the line immediately preceding the code being reviewed
   d. If multiple issues exist in a file, combine them into a single replace_file_content call with multiple ReplacementChunks
