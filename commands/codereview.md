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
