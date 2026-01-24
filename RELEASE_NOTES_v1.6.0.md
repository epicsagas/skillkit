# SkillKit v1.6.0 - Advanced Platform Features & Robustness

This is a major feature release that transforms SkillKit into a comprehensive agentic development platform with methodologies, orchestration, planning, hooks, and advanced features.

## 🚀 New Features

### Methodology Framework (Phase 12)
- **5 Development Methodologies**: Collaboration, Debugging, Meta, Planning, Testing
- **13 Curated Skills**: Professional development workflows for teams
- Methodology validation and management system
- CLI commands: `skillkit methodology list|load|apply`
- TUI Methodology screen (key: `o`)

### Hooks & Automation (Phase 13)
- **Event-Driven System**: Auto-trigger skills on file changes, git events
- Hook registry with enable/disable controls
- Git hooks, file watchers, command hooks
- CLI commands: `skillkit hook list|register|trigger|enable|disable`

### Multi-Agent Orchestration (Phase 14)
- **Team Coordination**: Assign tasks to multiple AI agents
- Task management with dependencies
- Agent messaging and communication
- Task statistics and progress tracking

### Plan System (Phase 15)
- **Structured Plans**: Parse, validate, execute development plans
- Plan parser with markdown support
- Plan validator for structure and dependencies
- Plan executor with pause/resume/cancel
- CLI commands: `skillkit plan parse|validate|execute|status`
- TUI Plan screen (key: `n`)

### Slash Commands (Phase 16)
- **Agent-Native Commands**: Generate slash commands for supported agents
- Command registry and management
- Command generation with examples
- CLI commands: `skillkit command generate|list`

### Agent Features (Phase 17)
- **Bootstrap Instructions**: Auto-load context on agent startup
- **Glob Patterns**: Apply skills conditionally based on file patterns
- **Modes**: Execution modes (interactive, autonomous, review)
- **Permissions**: Fine-grained control over agent capabilities
- Full feature validation and testing

### AI-Powered Features (Phase 18)
- **AI Manager**: Pluggable AI provider system
- **Code Generation**: AI-assisted code generation
- **Smart Search**: AI-powered skill discovery
- Mock provider for testing

### Audit Logging
- Comprehensive audit trail for all operations
- Query and export audit logs
- Event filtering and statistics
- CLI commands: `skillkit audit list|query|export|stats`

## 🐛 Bug Fixes & Improvements

### Code Quality
- Fixed minimatch dependency for Node 18 compatibility (downgraded to ^9.0.0)
- Fixed command export path handling with directory support
- Fixed command merge logic to preserve curated examples
- Fixed plan comma-separated input filtering
- Fixed audit logger pagination with explicit 0 values
- Fixed audit logger JSON parse error handling
- Fixed team orchestrator idle agent selection
- Fixed plan executor cancel/waitIfPaused hanging
- Fixed plan techStack filtering in createPlan
- Fixed executor abort checks after pause resume

### Robustness
- Graceful handling of malformed JSON in audit logs
- Proper abort signal checking in plan execution
- Prevention of task assignment to non-idle agents
- Empty string filtering in comma-separated inputs
- Directory path handling with automatic parent creation

## 📚 Documentation
- Updated all package READMEs with Phase 10-18 features
- Added comprehensive usage examples
- Added CLI command documentation
- Added TUI screen descriptions
- Clean, user-focused documentation without marketing fluff

## 🧪 Testing
- 639 tests passing across all packages
- Full test coverage for new features
- Integration tests for orchestration and planning
- Feature validation tests for agent features

## 📦 Packages Updated
- `@skillkit/core@1.6.0`
- `@skillkit/cli@1.6.0`
- `@skillkit/tui@1.6.0`
- `@skillkit/agents@1.6.0`
- `skillkit@1.6.0`

## 🔧 Installation

```bash
npm install -g skillkit@1.6.0
```

## 📖 Full Changelog
See commit history for detailed changes: https://github.com/rohitg00/skillkit/compare/v1.5.0...v1.6.0
