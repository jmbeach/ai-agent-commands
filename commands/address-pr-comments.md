1. Ask for url to PR
2. Determine the following variables from the url:
    - repo_owner
    - repo_name
    - pr_id
3. Get comments on PR

 bash
  gh api --paginate repos/[repo_owner]/[repo_name]/pulls/[pr_id]/comments | jq '.[] | {user: .user.login, body, path, line, original_line, created_at, in_reply_to_id, pull_request_review_id, commit_id, seen: .reactions.eyes} | select(.seen == 0)'

4. For EACH comment, do the following. Remember to address one comment at a time.
 a. Print out the following: "(index). From [user] on [file]:[lines] — [body]"
 b. Analyze the file and the line range.
 c. If you don't understand the comment, do not make a change. Just ask me for clarification, or to implement it myself.
 d. If you think you can make the change, make the change BEFORE moving onto the next comment.

5. After all comments are processed, summarize what you did, and which comments need the USER's attention.
