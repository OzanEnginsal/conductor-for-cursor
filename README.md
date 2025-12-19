# Conductor for Cursor

**Measure twice, code once.**

Conductor for Cursor is a Context-Driven Development system that brings structured planning and implementation workflows to Cursor IDE. Inspired by the [Conductor extension for Gemini CLI](https://github.com/gemini-cli-extensions/conductor), this project provides custom Cursor commands that enable you to specify, plan, and implement software features with consistency and quality.

Instead of just writing code, Conductor ensures a consistent, high-quality lifecycle for every task: **Context → Spec & Plan → Implement**.

## Philosophy

The philosophy behind Conductor is simple: control your code. By treating context as a managed artifact alongside your code, you transform your repository into a single source of truth that drives every agent interaction with deep, persistent project awareness.

## Features

* **Plan before you build**: Create specs and plans that guide the agent for new and existing codebases.
* **Maintain context**: Ensure AI follows style guides, tech stack choices, and product goals.
* **Iterate safely**: Review plans before code is written, keeping you firmly in the loop.
* **Work as a team**: Set project-level context for your product, tech stack, and workflow preferences that become a shared foundation for your team.
* **Build on existing projects**: Intelligent initialization for both new (Greenfield) and existing (Brownfield) projects.
* **Smart revert**: A git-aware revert command that understands logical units of work (tracks, phases, tasks) rather than just commit hashes.

## Installation

1. Clone or download this repository
2. Copy the `.cursor/commands/` directory to your project root
3. Copy the `conductor/` directory to your project root (or let the `/setup` command create it)

That's it! The commands will be available in Cursor when you type `/` in the chat.

## Usage

Conductor is designed to manage the entire lifecycle of your development tasks.

### 1. Set Up the Project (Run Once)

When you run `/setup`, Conductor helps you define the core components of your project context. This context is then used for building new components or features by you or anyone on your team.

* **Product**: Define project context (e.g. users, product goals, high-level features).
* **Product guidelines**: Define standards (e.g. prose style, brand messaging, visual identity).
* **Tech stack**: Configure technical preferences (e.g. language, database, frameworks).
* **Workflow**: Set team preferences (e.g. TDD, commit strategy). Uses workflow.md as a customizable template.

**Generated Artifacts:**

* `conductor/product.md`
* `conductor/product-guidelines.md`
* `conductor/tech-stack.md`
* `conductor/workflow.md`
* `conductor/code_styleguides/`
* `conductor/tracks.md`

```
/setup
```

### 2. Start a New Track (Feature or Bug)

When you're ready to take on a new feature or bug fix, run `/new-track`. This initializes a **track** — a high-level unit of work. Conductor helps you generate two critical artifacts:

* **Specs**: The detailed requirements for the specific job. What are we building and why?
* **Plan**: An actionable to-do list containing phases, tasks, and sub-tasks.

**Generated Artifacts:**

* `conductor/tracks/<track_id>/spec.md`
* `conductor/tracks/<track_id>/plan.md`
* `conductor/tracks/<track_id>/metadata.json`

```
/new-track
# OR with a description
/new-track "Add a dark mode toggle to the settings page"
```

### 3. Implement the Track

Once you approve the plan, run `/implement`. Your coding agent then works through the `plan.md` file, checking off tasks as it completes them.

**Updated Artifacts:**

* `conductor/tracks.md` (Status updates)
* `conductor/tracks/<track_id>/plan.md` (Status updates)
* Project context files (Synchronized on completion)

```
/implement
```

Conductor will:

1. Select the next pending task.
2. Follow the defined workflow (e.g., TDD: Write Test → Fail → Implement → Pass).
3. Update the status in the plan as it progresses.
4. **Verify Progress**: Guide you through a manual verification step at the end of each phase to ensure everything works as expected.

During implementation, you can also:

* **Check status**: Get a high-level overview of your project's progress.  
```
/status
```
* **Revert work**: Undo a feature or a specific task if needed.  
```
/revert
```

## Commands Reference

| Command | Description | Artifacts |
|---------|-------------|-----------|
| `/setup` | Scaffolds the project and sets up the Conductor environment. Run this once per project. | `conductor/product.md`<br>`conductor/tech-stack.md`<br>`conductor/workflow.md`<br>`conductor/tracks.md` |
| `/new-track` | Starts a new feature or bug track. Generates spec.md and plan.md. | `conductor/tracks/<id>/spec.md`<br>`conductor/tracks/<id>/plan.md`<br>`conductor/tracks.md` |
| `/implement` | Executes the tasks defined in the current track's plan. | `conductor/tracks.md`<br>`conductor/tracks/<id>/plan.md` |
| `/status` | Displays the current progress of the tracks file and active tracks. | Reads `conductor/tracks.md` |
| `/revert` | Reverts a track, phase, or task by analyzing git history. | Reverts git history |

## Directory Structure

```
.cursor/
  commands/
    setup.md
    new-track.md
    implement.md
    status.md
    revert.md
conductor/
  product.md
  product-guidelines.md
  tech-stack.md
  workflow.md
  tracks.md
  code_styleguides/
    (directory for style guide files)
  tracks/
    <track_id>/
      spec.md
      plan.md
      metadata.json
```

## Differences from Conductor (Gemini CLI)

This Cursor adaptation maintains the core philosophy and workflow of Conductor but is adapted for Cursor's command system:

* **Command Format**: Uses Cursor's markdown-based command files instead of Gemini CLI extension format
* **Integration**: Works seamlessly with Cursor's AI chat interface
* **No Extension Installation**: Simply copy files to your project - no extension installation needed
* **Team Sharing**: Commands are version-controlled with your project, making them easy to share

## Inspiration

This project is inspired by [Conductor](https://github.com/gemini-cli-extensions/conductor), a Gemini CLI extension for Context-Driven Development. While this adaptation maintains the core concepts and workflow, it has been redesigned specifically for Cursor IDE's command system.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

Licensed under the Apache License, Version 2.0. See [LICENSE](LICENSE) for details.

## Acknowledgments

* Inspired by [Conductor](https://github.com/gemini-cli-extensions/conductor) by gemini-cli-extensions
* Built for [Cursor IDE](https://cursor.sh/)
