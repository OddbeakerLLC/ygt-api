# LLM Instructions - YGT Integration

## Startup Process

At the start of every conversation, follow this process to integrate with YouGotThis! (YGT) task management:

### 1. Check for ygt-api Installation

First, check if the `ygt-api` bash script is available in the system PATH:

```bash
which ygt-api
```

**If NOT in PATH:**

- Ask the user: "The ygt-api tool is not installed. Would you like me to install it from https://github.com/OddbeakerLLC/ygt-api?"
- If they agree:
  - Clone the repository
  - Run the install script
  - Ask the user to run `ygt-api --init` in their project directory
  - Restart this entire startup process from the beginning

**If ygt-api is in PATH, proceed to step 2.**

### 2. Check for Project Configuration

**Important:** Before proceeding, this file (YGT.md) must exist in the project directory. If it doesn't exist, the user needs to run:

```bash
ygt-api --init
```

This command will copy YGT.md to the current directory (with a check for .git to confirm it's a code project).

Once YGT.md exists, look for `.claude-ygt.json` in the current working directory.

**If the file does NOT exist:**

a. Fetch the user's places (workspaces):

```bash
ygt-api GET /context ""
```

b. Display the available places and ask the user to choose one.

c. Fetch all unfinished projects from the selected place:

```bash
ygt-api GET "/projects?place_id={place_id}"
```

Note: Only show projects where `complete` is `false` or `0`.

d. Display the available projects and ask the user to choose one.

e. Save the selected Place ID and Project ID to `.claude-ygt.json`:

```json
{
  "place_id": "123",
  "project_id": "456"
}
```

**If the file exists, load the Place and Project IDs from it.**

### 3. Load Active Tasks

a. First, attempt to load tasks from the "today" pile:

```bash
ygt-api GET "/tasks?project_id={project_id}&pile=today"
```

b. If there are NO tasks in the today pile, load tasks from the "future" pile:

```bash
ygt-api GET "/tasks?project_id={project_id}&pile=future"
```

c. Display the task list to the user with a message like:
"Here are your active YGT tasks for this project. How can I help you with them?"

### 4. Token Error Handling

If at ANY point during this process `ygt-api` returns an error about the token (expired, invalid, missing, authentication failed, etc.):

- Ask the user: "Your YGT access token is missing or expired. Please provide a new YGT Access Token."
- When they provide the token, use it as the 4th parameter in all subsequent `ygt-api` calls:
  ```bash
  ygt-api GET /context "" {token}
  ygt-api GET "/tasks?project_id=123&pile=today" "" {token}
  ```
- Continue from where the error occurred.

## Notes

- YGT tokens expire with each YGT session, so token errors are common and expected.
- The `.claude-ygt.json` file links the current code project to a specific YGT project.
- Task piles in YGT: `today` (active work), `future` (backlog), `completed` (done), `scheduled` (date-specific).
- Always pass `""` as the 3rd parameter for GET requests when providing a token as the 4th parameter.
