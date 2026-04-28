# Recommended Coding Agents Other Than Claw Code

This page summarizes the coding agents that are worth considering besides Claw Code, based on `deep-research/coding-agents-recommendation.md`.

The short version is: **Cline is the best all-around choice, and OpenCode is the strongest choice for flexibility**.  
If you want a simple CLI workflow, **Aider is still a strong option**.  
**Roo Code is not recommended for new adoption** because its official shutdown notice makes long-term use a poor bet.  
Claw Code itself looks promising, but it is still more of a tracking target than a mature default choice.

## Quick Ranking

1. **Cline**
   - Best overall balance of features, documentation, and workflow integration.
   - Strong for MCP, Marketplace, Skills, session resume, and checkpoints.
2. **OpenCode**
   - Best for provider flexibility and extensibility.
   - Strong if you want OpenAI-compatible endpoints, custom providers, plugins, skills, and MCP.
3. **Aider**
   - Strong CLI-first coding assistant.
   - Best when you want a simple, dependable tool rather than a large extension ecosystem.
4. **Roo Code**
   - Feature-rich, but not a good new investment because the project has an official shutdown timeline.

## What Makes Each One Worth Considering

### Cline

Cline is the **best balanced option overall**.

- It combines MCP, Marketplace, Skills, resume, and checkpoints into one workflow.
- It is well suited for IDE-centric usage.
- It has a strong documentation story and a mature feature surface.

Best for:

- Users who want a first choice with the fewest compromises
- Teams that need a tool they can explain and standardize
- Developers who prefer IDE integration

Caveats:

- Web Tools depend on provider-specific conditions, so model swapping can change behavior.
- There are known issues around checkpoints, terminal integration, and MCP.

### OpenCode

OpenCode is the **most flexible and extensible** option in this group.

- It supports 75+ providers.
- You can add arbitrary OpenAI-compatible providers.
- It includes plugins, skills, MCP, websearch, webfetch, and a headless server path.
- Session continuation, branching, sharing, and API-oriented workflows are built in.

Best for:

- Users who want the broadest provider choice
- Terminal-first workflows
- Power users who want to extend the agent stack themselves

Caveats:

- Compaction and model discovery still have rough edges.
- The higher flexibility shifts more configuration responsibility to the user.

### Aider

Aider is a **practical CLI coding companion**.

- Its `/ask`, `/architect`, and `/web` modes are straightforward.
- It supports read-focused workflows well.
- OpenRouter model slugs can be used directly.

Best for:

- Users who want a simple, low-surprise CLI tool
- Pair-programming in the terminal
- Small edits and steady day-to-day coding work

Caveats:

- It is weak on MCP, plugins, skills, and marketplace-style extensibility.
- It does not match Cline or OpenCode on the broader feature checklist.

### Roo Code

Roo Code is still **feature-rich on paper**.

- It has strong provider/profile switching.
- Skills, Marketplace, checkpoints, and context condensing are all present.
- Existing users may still find it comfortable.

However, **it is not recommended for new adoption because the official shutdown notice changes the risk profile.**

## Practical Selection Guide

- **Best first choice**: Cline
- **Best for flexibility**: OpenCode
- **Best for CLI simplicity**: Aider
- **Best as a migration target**: Roo Code
- **Best as an experimental track**: Claw Code

## Short Recommendation

If you only want one answer:

1. Choose **Cline** if you want the strongest all-around product.
2. Choose **OpenCode** if provider freedom and extensibility matter most.
3. Choose **Aider** if you want a lean CLI workflow with minimal overhead.

## Notes

This comparison is conservative when the official documentation does not clearly confirm a feature.  
That tends to favor Cline and OpenCode because their docs are more complete.  
Also, **being usable with OpenRouter is not the same as preserving every feature under OpenRouter**. When you swap providers, check surrounding features such as Web Tools and compaction behavior.
