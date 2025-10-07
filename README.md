# YGT API Wrapper

A bash script wrapper for the [YouGotThis!](https://ygt.oddbeaker.com) (YGT) task management API. Provides a simple command-line interface for managing projects, tasks, and bookmarks.

## Prerequisites

- bash
- curl
- A YouGotThis! account and API token

### Getting Access to YGT

1. Visit [https://ygt.oddbeaker.com](https://ygt.oddbeaker.com)
2. Sign up for an account or log in
3. Obtain your API token from the YGT interface
   - **Note**: Tokens expire with each YGT session and must be updated regularly

## Installation

```bash
# Clone the repository
git clone <your-repo-url>
cd ygt-api

# Run the installation script
./install
```

The install script creates a symlink in `/usr/local/bin`, making `ygt-api` available system-wide.

**Manual installation** (if you prefer):
```bash
chmod +x ygt-api
# Either add to PATH or create symlink manually:
sudo ln -s "$(pwd)/ygt-api" /usr/local/bin/ygt-api
```

## Authentication

Set your YGT API token as an environment variable:

```bash
export YGT_TOKEN="your_token_here"
```

Alternatively, pass the token as the fourth argument to each command:

```bash
./ygt-api GET /context "" "your_token_here"
```

## Usage

```bash
./ygt-api <method> <endpoint> [data] [token]
```

### Arguments

- **method** - HTTP method: `GET`, `POST`, or `PUT`
- **endpoint** - API endpoint path (e.g., `/context`, `/tasks`, `/projects`)
- **data** - JSON data for POST/PUT requests (optional for GET)
- **token** - YGT API token (optional if `YGT_TOKEN` env var is set)

## Available Endpoints

| Endpoint | Description |
|----------|-------------|
| `/context` | Get user info and places (workspaces) |
| `/projects` | List/create/update projects |
| `/projects/<id>` | Get/update specific project |
| `/tasks` | List/create/update tasks |
| `/tasks/<id>` | Get/update specific task |
| `/knowledge` | List/create/update bookmarks |
| `/knowledge/<id>` | Get/update specific bookmark |

## Task Pile System

YGT organizes tasks into "piles" for workflow management:

- **today** - Current active work (default)
- **future** - Backlog items
- **completed** - Finished tasks
- **scheduled** - Tasks with specific dates

## Examples

### Get context (user info and places)
```bash
./ygt-api GET /context
```

### List all projects
```bash
./ygt-api GET /projects
```

### List today's tasks
```bash
./ygt-api GET "/tasks?pile=today"
```

### List future tasks with limit
```bash
./ygt-api GET "/tasks?pile=future&limit=10"
```

### Create a new project
```bash
./ygt-api POST /projects '{"place_id":"166","title":"My Project","description":"Project description"}'
```

### Create a new task
```bash
./ygt-api POST /tasks '{"project_id":"471","title":"Do something","pile":"today","description":"Task details"}'
```

### Move task to completed
```bash
./ygt-api PUT /tasks/123 '{"pile":"completed"}'
```

### Update project title
```bash
./ygt-api PUT /projects/471 '{"title":"Updated Project Name"}'
```

### Create a bookmark
```bash
./ygt-api POST /knowledge '{"place_id":"166","title":"Useful Link","url":"https://example.com","description":"Optional description"}'
```

## Query Parameters

### GET /tasks
- `pile` - Filter by pile (default: "today")
- `project_id` - Filter by project
- `place_id` - Filter by place
- `limit` - Maximum results (default: 50)

### GET /projects
- `place_id` - Filter by place
- `include_completed` - Include completed projects

### GET /knowledge
- `place_id` - Filter by place

## Required Fields

### POST /projects
- `title` (required)
- `place_id` (required)
- `description` (optional)

### POST /tasks
- `title` (required)
- `project_id` (required)
- `pile` (optional)
- `description` (optional)
- `scheduled_date` (required if pile is "scheduled")

### POST /knowledge
- `title` (required)
- `url` (required)
- `place_id` (required)
- `description` (optional)

## Workflow Tips

1. **Start with context** to see available places:
   ```bash
   ./ygt-api GET /context
   ```

2. **List projects** to find project IDs:
   ```bash
   ./ygt-api GET /projects
   ```

3. **Create tasks** with the project_id:
   ```bash
   ./ygt-api POST /tasks '{"project_id":"471","title":"My Task","pile":"today"}'
   ```

4. **Move tasks between piles** as work progresses:
   ```bash
   ./ygt-api PUT /tasks/123 '{"pile":"completed"}'
   ```

## Response Format

All responses are returned as JSON from the YGT API.

**Context response** includes:
- `user`: {id, name, email}
- `places`: [{id, name, is_editable, is_sharable}]

**Projects** include:
- id, title, description
- place: {id, name}
- task_count, completed_tasks, progress_percent
- dates: {created, completed}

**Tasks** include:
- id, title, description, pile
- project: {id, name, place_id}
- dates: {created, show, end}

## Help

View the built-in help documentation:

```bash
./ygt-api --help
```

## Error Handling

The script validates required fields before making API calls and returns descriptive error messages for:
- Missing authentication token
- Missing required fields in POST requests
- Invalid HTTP methods
- Missing data for POST/PUT operations

## License

[Add your license here]

## Contributing

[Add contributing guidelines here]
