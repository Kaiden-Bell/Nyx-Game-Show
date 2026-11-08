## User Approval

- The user owns gameplay and product decisions and has final say on implementation scope.
- Before implementing code, explain the problem, proposed change, why it is needed, and the expected impact. Wait for the user's explicit approval before editing or creating code.
- If the scope or approach changes materially after approval, explain the change and ask again before proceeding.
- Do not silently decide open questions in the GDD; present options and ask the user.

## Coding Restrictions
For gameplay code changes:

- delegate implementation to a coding agent. 
- After it finishes, have a separate, read-only reviewer agent inspect the requirements and code diff independently. 
- Give the reviewer the task, relevant GDD sections, and diff—not the coder’s explanation. 
- If it finds issues, send those back to the coder to fix, then review again.

