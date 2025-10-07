# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This repository contains `ygt-api` - a bash script wrapper for the YouGotThis! (YGT) API, a task management system. The script provides a command-line interface for interacting with the YGT API endpoints.

## Architecture

**Single Script Design**: The entire functionality is contained in a single bash script ([ygt-api](ygt-api)) that acts as a curl wrapper with built-in validation and help documentation.

**API Structure**: The script interfaces with `https://ygt.oddbeaker.com/api/llm.php` and supports three HTTP methods (GET, POST, PUT) across multiple endpoints:
- `/context` - User info and workspaces (called "places")
- `/projects` - Project management
- `/tasks` - Task management with pile system
- `/knowledge` - Bookmark management

**Authentication**: Uses bearer token authentication via `YGT_TOKEN` environment variable or command-line argument. Tokens expire with each YGT session.

**Task Pile System**: YGT organizes tasks into "piles" for workflow management:
- `today` - Current active work
- `future` - Backlog items
- `completed` - Finished tasks
- `scheduled` - Date-specific tasks

## Development Commands

### Running the script
```bash
./ygt-api <method> <endpoint> [data] [token]
```

### Testing different endpoints
```bash
# Get context (requires YGT_TOKEN set)
./ygt-api GET /context

# List tasks
./ygt-api GET "/tasks?pile=today"

# Create a task
./ygt-api POST /tasks '{"project_id":"471","title":"New Task","pile":"today"}'

# Update a task
./ygt-api PUT /tasks/123 '{"pile":"completed"}'
```

## Key Implementation Details

**Validation Logic**: The script includes request validation (lines 148-208) that checks:
- Required fields for POST requests (title, place_id, project_id, url)
- Conditional requirements (scheduled_date required if pile is "scheduled")
- Data presence for POST/PUT operations

**URL Building**: Query parameters are handled by checking if the endpoint already contains `?` to properly append the token parameter (lines 211-215).

**Error Handling**: The script validates inputs before making API calls and returns descriptive error messages to stderr.
