# Memory System - Long-Term Context

Persistent memory system for maintaining context across sessions.

## How It Works
This system stores and retrieves user preferences, project context, and learned information across sessions.

## Memory Categories
- **User Profile**: Name, role, company, preferences
- **Project Context**: Active projects, goals, status
- **Technical Preferences**: Stacks, tools, conventions
- **Business Context**: Clients, partners, revenue streams
- **Learning**: Skills acquired, lessons learned
- **Decisions**: Key decisions and rationale

## Storage
Memory is stored in structured markdown files within the `.opencode/memory/` directory.
- `profile.md` - User profile and preferences
- `projects.md` - Active project context
- `learnings.md` - Lessons and insights
- `decisions.md` - Decision log

## Usage
Always check memory files at the start of each session. Update memory files when new information is learned.
