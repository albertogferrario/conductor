---
name: dev:help
description: Show available Conductor commands and usage guide
---

<objective>
Display the complete Conductor command reference.

Output ONLY the reference content below. Do NOT add:

- Project-specific analysis
- Git status or file context
- Next-step suggestions
- Any commentary beyond the reference
  </objective>

<reference>
# Conductor Command Reference

**Conductor** creates hierarchical project plans optimized for solo agentic development with Claude Code.

## Quick Start

1. `/dev:new-project` - Initialize project with brief
2. `/dev:create-roadmap` - Create roadmap and phases
3. `/dev:plan-phase <number>` - Create detailed plan for first phase
4. `/dev:execute-plan <path>` - Execute the plan

## Core Workflow

```
Initialization → Planning → Execution → Milestone Completion
```

### Project Initialization

**`/dev:new-project`**
Initialize new project with brief and configuration.

- Creates `.planning/PROJECT.md` (vision and requirements)
- Creates `.planning/config.json` (workflow mode)
- Asks for workflow mode (interactive/yolo) upfront
- Commits initialization files to git

Usage: `/dev:new-project`

**`/dev:create-roadmap`**
Create roadmap and state tracking for initialized project.

- Creates `.planning/ROADMAP.md` (phase breakdown)
- Creates `.planning/STATE.md` (project memory)
- Creates `.planning/phases/` directories

Usage: `/dev:create-roadmap`

**`/dev:map-codebase`**
Map an existing codebase for brownfield projects.

- Analyzes codebase with parallel Explore agents
- Creates `.planning/codebase/` with 7 focused documents
- Covers stack, architecture, structure, conventions, testing, integrations, concerns
- Use before `/dev:new-project` on existing codebases

Usage: `/dev:map-codebase`

### Phase Planning

**`/dev:discuss-phase <number>`**
Help articulate your vision for a phase before planning.

- Captures how you imagine this phase working
- Creates CONTEXT.md with your vision, essentials, and boundaries
- Use when you have ideas about how something should look/feel

Usage: `/dev:discuss-phase 2`

**`/dev:research-phase <number>`**
Comprehensive ecosystem research for niche/complex domains.

- Discovers standard stack, architecture patterns, pitfalls
- Creates RESEARCH.md with "how experts build this" knowledge
- Use for 3D, games, audio, shaders, ML, and other specialized domains
- Goes beyond "which library" to ecosystem knowledge

Usage: `/dev:research-phase 3`

**`/dev:list-phase-assumptions <number>`**
See what Claude is planning to do before it starts.

- Shows Claude's intended approach for a phase
- Lets you course-correct if Claude misunderstood your vision
- No files created - conversational output only

Usage: `/dev:list-phase-assumptions 3`

**`/dev:plan-phase <number>`**
Create detailed execution plan for a specific phase.

- Generates `.planning/phases/XX-phase-name/XX-YY-PLAN.md`
- Breaks phase into concrete, actionable tasks
- Includes verification criteria and success measures
- Multiple plans per phase supported (XX-01, XX-02, etc.)

Usage: `/dev:plan-phase 1`
Result: Creates `.planning/phases/01-foundation/01-01-PLAN.md`

### Execution

**`/dev:execute-plan <path>`**
Execute a single PLAN.md file.

- Runs plan tasks sequentially
- Creates SUMMARY.md after completion
- Updates STATE.md with accumulated context
- Use for interactive execution with checkpoints

Usage: `/dev:execute-plan .planning/phases/01-foundation/01-01-PLAN.md`

**`/dev:execute-phase <phase-number>`**
Execute all unexecuted plans in a phase with parallel background agents.

- Analyzes plan dependencies and spawns independent plans concurrently
- Use when phase has 2+ plans and you want "walk away" execution
- Respects max_concurrent_agents from config.json

Usage: `/dev:execute-phase 5`

Options (via `.planning/config.json` parallelization section):
- `max_concurrent_agents`: Limit parallel agents (default: 3)
- `skip_checkpoints`: Skip human checkpoints in background (default: true)
- `min_plans_for_parallel`: Minimum plans to trigger parallelization (default: 2)

**`/dev:status [--wait]`**
Check status of background agents from parallel execution.

- Shows running/completed agents from agent-history.json
- Uses TaskOutput to poll agent status
- With `--wait`: blocks until all agents complete

Usage: `/dev:status` or `/dev:status --wait`

### Roadmap Management

**`/dev:add-phase <description>`**
Add new phase to end of current milestone.

- Appends to ROADMAP.md
- Uses next sequential number
- Updates phase directory structure

Usage: `/dev:add-phase "Add admin dashboard"`

**`/dev:insert-phase <after> <description>`**
Insert urgent work as decimal phase between existing phases.

- Creates intermediate phase (e.g., 7.1 between 7 and 8)
- Useful for discovered work that must happen mid-milestone
- Maintains phase ordering

Usage: `/dev:insert-phase 7 "Fix critical auth bug"`
Result: Creates Phase 7.1

**`/dev:remove-phase <number>`**
Remove a future phase and renumber subsequent phases.

- Deletes phase directory and all references
- Renumbers all subsequent phases to close the gap
- Only works on future (unstarted) phases
- Git commit preserves historical record

Usage: `/dev:remove-phase 17`
Result: Phase 17 deleted, phases 18-20 become 17-19

### Milestone Management

**`/dev:discuss-milestone`**
Figure out what you want to build in the next milestone.

- Reviews what shipped in previous milestone
- Helps you identify features to add, improve, or fix
- Routes to /dev:new-milestone when ready

Usage: `/dev:discuss-milestone`

**`/dev:new-milestone <name>`**
Create a new milestone with phases for an existing project.

- Adds milestone section to ROADMAP.md
- Creates phase directories
- Updates STATE.md for new milestone

Usage: `/dev:new-milestone "v2.0 Features"`

**`/dev:complete-milestone <version>`**
Archive completed milestone and prepare for next version.

- Creates MILESTONES.md entry with stats
- Archives full details to milestones/ directory
- Creates git tag for the release
- Prepares workspace for next version

Usage: `/dev:complete-milestone 1.0.0`

### Progress Tracking

**`/dev:progress`**
Check project status and intelligently route to next action.

- Shows visual progress bar and completion percentage
- Summarizes recent work from SUMMARY files
- Displays current position and what's next
- Lists key decisions and open issues
- Offers to execute next plan or create it if missing
- Detects 100% milestone completion

Usage: `/dev:progress`

### Session Management

**`/dev:resume-work`**
Resume work from previous session with full context restoration.

- Reads STATE.md for project context
- Shows current position and recent progress
- Offers next actions based on project state

Usage: `/dev:resume-work`

**`/dev:pause-work`**
Create context handoff when pausing work mid-phase.

- Creates .continue-here file with current state
- Updates STATE.md session continuity section
- Captures in-progress work context

Usage: `/dev:pause-work`

### Debugging

**`/dev:debug [issue description]`**
Systematic debugging with persistent state across context resets.

- Gathers symptoms through adaptive questioning
- Creates `.planning/debug/[slug].md` to track investigation
- Investigates using scientific method (evidence → hypothesis → test)
- Survives `/clear` — run `/dev:debug` with no args to resume
- Archives resolved issues to `.planning/debug/resolved/`

Usage: `/dev:debug "login button doesn't work"`
Usage: `/dev:debug` (resume active session)

### Todo Management

**`/dev:add-todo [description]`**
Capture idea or task as todo from current conversation.

- Extracts context from conversation (or uses provided description)
- Creates structured todo file in `.planning/todos/pending/`
- Infers area from file paths for grouping
- Checks for duplicates before creating
- Updates STATE.md todo count

Usage: `/dev:add-todo` (infers from conversation)
Usage: `/dev:add-todo Add auth token refresh`

**`/dev:check-todos [area]`**
List pending todos and select one to work on.

- Lists all pending todos with title, area, age
- Optional area filter (e.g., `/dev:check-todos api`)
- Loads full context for selected todo
- Routes to appropriate action (work now, add to phase, brainstorm)
- Moves todo to done/ when work begins

Usage: `/dev:check-todos`
Usage: `/dev:check-todos api`

### Utility Commands

**`/dev:help`**
Show this command reference.

**`/dev:whats-new`**
See what's changed since your installed version.

- Shows installed vs latest version comparison
- Displays changelog entries for versions you've missed
- Highlights breaking changes
- Provides update instructions when behind

Usage: `/dev:whats-new`

## Files & Structure

```
.planning/
├── PROJECT.md            # Project vision
├── ROADMAP.md            # Current phase breakdown
├── STATE.md              # Project memory & context
├── config.json           # Workflow mode & gates
├── todos/                # Captured ideas and tasks
│   ├── pending/          # Todos waiting to be worked on
│   └── done/             # Completed todos
├── debug/                # Active debug sessions
│   └── resolved/         # Archived resolved issues
├── codebase/             # Codebase map (brownfield projects)
│   ├── STACK.md          # Languages, frameworks, dependencies
│   ├── ARCHITECTURE.md   # Patterns, layers, data flow
│   ├── STRUCTURE.md      # Directory layout, key files
│   ├── CONVENTIONS.md    # Coding standards, naming
│   ├── TESTING.md        # Test setup, patterns
│   ├── INTEGRATIONS.md   # External services, APIs
│   └── CONCERNS.md       # Tech debt, known issues
└── phases/
    ├── 01-foundation/
    │   ├── 01-01-PLAN.md
    │   └── 01-01-SUMMARY.md
    └── 02-core-features/
        ├── 02-01-PLAN.md
        └── 02-01-SUMMARY.md
```

## Workflow Modes

Set during `/dev:new-project`:

**Interactive Mode**

- Confirms each major decision
- Pauses at checkpoints for approval
- More guidance throughout

**YOLO Mode**

- Auto-approves most decisions
- Executes plans without confirmation
- Only stops for critical checkpoints

Change anytime by editing `.planning/config.json`

## Common Workflows

**Starting a new project:**

```
/dev:new-project
/dev:create-roadmap
/dev:plan-phase 1
/dev:execute-plan .planning/phases/01-foundation/01-01-PLAN.md
```

**Resuming work after a break:**

```
/dev:progress  # See where you left off and continue
```

**Adding urgent mid-milestone work:**

```
/dev:insert-phase 5 "Critical security fix"
/dev:plan-phase 5.1
/dev:execute-plan .planning/phases/05.1-critical-security-fix/05.1-01-PLAN.md
```

**Completing a milestone:**

```
/dev:complete-milestone 1.0.0
/dev:new-project  # Start next milestone
```

**Capturing ideas during work:**

```
/dev:add-todo                    # Capture from conversation context
/dev:add-todo Fix modal z-index  # Capture with explicit description
/dev:check-todos                 # Review and work on todos
/dev:check-todos api             # Filter by area
```

**Debugging an issue:**

```
/dev:debug "form submission fails silently"  # Start debug session
# ... investigation happens, context fills up ...
/clear
/dev:debug                                    # Resume from where you left off
```

## Getting Help

- Read `.planning/PROJECT.md` for project vision
- Read `.planning/STATE.md` for current context
- Check `.planning/ROADMAP.md` for phase status
- Run `/dev:progress` to check where you're up to
  </reference>
