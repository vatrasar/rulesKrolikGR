---
name: skill-creation
description: Provides guidelines and structure for creating new AI agent skills, including folder organization and SKILL.md requirements.
---

## When to use this skill
Use this skill when you are asked to create a new skill for an AI agent. It ensures that the skill follows the required structure and best practices for discoverability and maintainability.

## How to use it
1. **Determine Location:** Prepare the folder structure based on the user's needs.
   - Workspace-specific: `<workspace-root>/.agents/skills/<skill-folder-name>/` (Recommended for project-specific workflows).
2. **Create Structure:**
   - The `SKILL.md` file is mandatory.
   - Optional subdirectories: `scripts/` (helper scripts), `examples/` (reference implementations), `resources/` (templates/assets).
3. **Draft SKILL.md:**
   - Start with the required YAML frontmatter:
     ```yaml
     ---
     name: [unique-skill-name]
     description: [clear-description-of-what-the-skill-does]
     ---
     ```
   - Write the description in the third person, including relevant keywords.
   - Include detailed instructions in Markdown (e.g., ## When to use this skill, ## How to use it).

## Best Practices
- **Keep skills focused:** Each skill should do one thing well. Create separate skills for distinct tasks.
- **Scripts as black boxes:** If the skill includes scripts, instruct the agent to run them with `--help` first rather than reading the entire source code.
- **Include decision trees:** For complex skills, add a section to help the agent choose the right approach based on the situation.
