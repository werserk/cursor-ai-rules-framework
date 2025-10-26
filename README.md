# Cursor AI Rules Framework

A comprehensive, evidence-first prompting framework designed to elevate AI agents from simple command executors to **Autonomous Principal Engineers**.

## 🎯 Philosophy

**Autonomy through discipline. Trust through verification.**

This framework enforces a rigorous workflow of reconnaissance, planning, safe execution, and self-improvement, ensuring every action is deliberate, verifiable, and aligned with senior engineering best practices.

## 📁 Framework Overview

### Core Components
- **`GLOBAL-RULES.mdc`** - The main operational doctrine. This is the "brain" of the Senior Engineer agent.
- **`framework/`** - Contains specialized, technology-specific rulesets.
- **`docs/`** - Context-aware documentation system (corporate vs startup).

### How It Works
This framework is not a library you install in your code. It's a set of instructions you install directly into your Cursor IDE settings. These rules guide the AI's behavior, making it more thorough, predictable, and safe.

## 🚀 How to Use: 2-Step Setup

### Step 1: Install the Core Doctrine (Mandatory)

This is the most important step. It installs the primary "operating system" for the AI.

1.  **Open Cursor Settings:** Go to `File > Preferences > Settings`.
2.  **Find AI Rules:** Search for "rules" and find the section for configuring AI behavior.
3.  **Create a Global Rule:** Add a new "Global Rule".
4.  **Copy & Paste:** Open the `GLOBAL-RULES.mdc` file from this repository, copy its entire content, and paste it into the global rule you just created in Cursor.

This single rule provides the foundational operational principles for all your projects.

### Step 2: Add Specialized Rules (Optional but Recommended)

Specialized rules fine-tune the AI's behavior for specific technologies.

1.  **Add Another Rule:** In the same Cursor settings section, add another rule.
2.  **Copy & Paste Tech-Specific Rules:**
    -   **For Python Projects:** Open `framework/python/PYTHON-RULES.mdc`, copy its content, and paste it into the new rule.
    -   **For Other Tech:** (As new rules are added to the `framework/` directory, you can add them here).
3.  **Set File Path Specificity (Optional):** You can tell Cursor to only apply a rule to certain files (e.g., apply the Python rules only to `.py` files).

## 📁 Framework Components

### Core File
- **`GLOBAL-RULES.mdc`** - The main framework and operational doctrine, based on the original work by `aashari`. This file contains the complete, detailed instructions for the AI to act as a Senior Engineer.

### Specialized Rulesets
- **`framework/python/PYTHON-RULES.mdc`** - A consolidated set of best practices for Python development, covering everything from dependency management with Poetry to testing with Pytest and secure coding practices.
- **`estensions/`** - Contains additional, highly specialized rules for domains like Jupyter Notebooks or Pine Script. These can be added as separate rules in Cursor as needed.

### Legacy Files

**Historical versions available in `OLD/` directory:**
- **`00 - Cursor AI Prompting Rules.md`** - Original framework documentation
- **`01 - core.md`** - Legacy core operational doctrine
- **`02 - request.md`** - Legacy standard operating procedure template
- **`03 - refresh.md`** - Legacy root cause analysis template
- **`04 - retro.md`** - Legacy self-improvement loop template
- **`05 - concise.md`** - Legacy concise communication rules
- **`06 - no-absolute-right.md`** - Legacy professional communication guidelines
- **`commits.mdc`** - Commit message guidelines

## 🔧 Workflow Example

After installing the rules, your workflow doesn't change much, but the AI's behavior will be radically different.

1.  **Give a Task:** Assign a task as you normally would, e.g., "Refactor the user authentication service to use a new caching layer."
2.  **Observe Disciplined Execution:** The AI will now follow the phases defined in `GLOBAL-RULES.mdc`:
    -   **Phase 0: Reconnaissance & Planning:** It will first research the codebase, identify relevant files, and present you with a detailed plan before making any changes.
    -   **Phase 1: Execution:** It will execute the plan step-by-step.
    -   **Phase 2: Testing & Verification:** It will run tests, lint checks, and manually verify its changes.
    -   **Phase 3: Self-Audit:** It will perform a final review of its own work against engineering best practices.
3.  **Review the Final Report:** The AI will provide a clear summary of its work, including evidence of its verification steps.

## 🎯 Status Markers

The framework uses clear status indicators:

- `✅` - Objective completed successfully
- `⚠️` - Recoverable issue encountered and fixed autonomously
- `🚧` - Blocked; awaiting input or resource

## 🧠 Core Principles

1. **Research-First, Always** - Never act on assumption
2. **Extreme Ownership** - Own end-to-end system health
3. **Autonomous Problem-Solving** - Self-sufficient operation
4. **Unyielding Precision & Safety** - Respect operational environment
5. **Metacognitive Self-Improvement** - Continuous learning and evolution

## 🔗 MCP Server Integration

The framework includes protocols for leveraging Model Context Protocol (MCP) servers:

- **Automatic Detection** - MCP servers globally configured
- **Context-Aware Selection** - Based on technology stack and requirements
- **Knowledge Synthesis** - Combine insights from multiple sources
- **Template Application** - Use MCP-provided best practices

## 📋 Cursor Plan Mode Compatibility

**Plan Mode (Shift+Tab) is fully compatible:**
- Generated plans follow Phase 0-5 structure
- Enhanced communication with structured overviews
- All operational protocols remain unchanged
- MCP server integration preserved

## 🤝 Contributing

This framework is designed for community use and improvement. Contributions are welcome for:

- New specialized rule files
- Framework enhancements
- Documentation improvements
- Bug fixes and optimizations

## 🙏 Credits & Attribution

This framework is inspired by and adapted from the original work of [aashari](https://gist.github.com/aashari/07cc9c1b6c0debbeb4f4d94a3a81339e). We thank the author for sharing their valuable insights and structures, which provided a solid foundation for developing this enhanced framework.

**Key modifications and enhancements:**
- Expanded operational doctrine with additional protocols
- Added specialized rules for notebooks, Pine Script, and analytics
- Integrated MCP server support protocols
- Enhanced communication guidelines and conciseness directives
- Added comprehensive documentation and usage examples
- **Integrated guidelines approach** - Feature-specific guidelines extend and integrate with general guidelines for complete coverage

## 📄 License

This framework is provided as-is for educational and professional use. Please respect the operational principles and maintain the disciplined approach that makes this framework effective.

---

**Welcome to a more disciplined, reliable, and truly autonomous way of working with AI.**

*Updated: 2025-10-26*
