# Conductor Systems for Cursor

**Measure twice, code once.**

This repository contains Context-Driven Development systems for Cursor IDE that bring structured planning and implementation workflows to your development process. Inspired by the [Conductor extension for Gemini CLI](https://github.com/gemini-cli-extensions/conductor), these systems enable you to specify, plan, and implement software features with consistency and quality.

## Overview

This project provides two specialized Conductor systems:

### [Base Conductor](conductor/README.md)

A general-purpose Context-Driven Development system for any software project. It helps you:

* Plan before you build with specs and actionable plans
* Maintain context through style guides, tech stack choices, and product goals
* Iterate safely with review-before-implementation workflows
* Work as a team with shared project-level context
* Build on existing projects with intelligent initialization
* Revert work intelligently using git-aware commands

**Ideal for**: General software development, feature development, bug fixes, and team collaboration.

### [API Conductor](api-conductor/README.md)

A specialized Conductor system optimized for building platform API connectors (LinkedIn, Twitter, GitHub, Stripe, etc.). It extends the base Conductor with:

* API-focused planning and workflows
* Built-in authentication patterns (OAuth 2.0, API keys)
* Standardized rate limiting and retry logic
* Consistent error handling across connectors
* Mock API testing infrastructure
* API-specific standards and templates

**Ideal for**: Building reliable, well-tested API connectors for third-party platforms.

## Quick Start

### Installing Base Conductor

1. Copy the `.cursor/commands/conductor/` directory to your project root
2. Copy the `conductor/` directory to your project root
3. Run `/setup` in Cursor to initialize your project

See the [Base Conductor README](conductor/README.md) for detailed documentation.

### Installing API Conductor

1. Copy the `.cursor/commands/api-conductor/` directory to your project root
2. Copy the `api-conductor/` directory to your project root
3. Run `/api-conductor-setup` in Cursor to initialize your project

See the [API Conductor README](api-conductor/README.md) for detailed documentation.

### Using Both

You can use both conductors in the same project if needed. They operate independently and can coexist without conflicts.

## Philosophy

The philosophy behind Conductor is simple: control your code. By treating context as a managed artifact alongside your code, you transform your repository into a single source of truth that drives every agent interaction with deep, persistent project awareness.

Instead of just writing code, Conductor ensures a consistent, high-quality lifecycle for every task: **Context → Spec & Plan → Implement**.

## Documentation

* **[Base Conductor Documentation](conductor/README.md)** - Complete guide for general-purpose Context-Driven Development
* **[API Conductor Documentation](api-conductor/README.md)** - Complete guide for API connector development

## License

Licensed under the Apache License, Version 2.0. See [LICENSE](LICENSE) for details.

## Acknowledgments

* Inspired by [Conductor](https://github.com/gemini-cli-extensions/conductor) by gemini-cli-extensions
* Built for [Cursor IDE](https://cursor.sh/)
