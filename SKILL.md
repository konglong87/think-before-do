---
name: think-before-code
description: Enforces thoughtful coding practices - think before coding, brevity first, surgical modifications, and goal-driven execution. Use this skill when starting any coding task, refactoring, bug fixing, or when the AI seems to be coding without sufficient analysis. This skill should be triggered when user says things like "help me code", "fix this bug", "refactor this", "implement this feature", or when the AI's approach seems too hasty.
---

# Think Before Code

A skill that enforces four core principles: think before acting, brevity first, surgical modifications, and goal-driven execution.

## When to Use This Skill

Trigger this skill at the beginning of any coding task:
- Implementing new features
- Fixing bugs
- Refactoring code
- Code reviews
- When the AI starts coding too quickly without analysis

This skill serves as a "thinking framework" that should be applied before any code is written.

## Core Principles

### Principle 1: Think Before Coding

To encode effectively, first understand deeply. Never pretend to understand everything.

**Rules:**
- When uncertain, ask questions. Never guess.
- When there is ambiguity, surface it. Never silently make a choice.
- When there is a simpler solution, speak up. Never silently follow a complex approach.
- When confused, stop and point out what is unclear.

**Red flags to watch for:**
- Starting to write code without understanding the requirement
- Making assumptions about edge cases
- Not asking clarifying questions when the spec is vague

### Principle 2: Brevity First

To write good code, maximize simplicity. Never do speculative work.

**Rules:**
- Never add features that were not requested
- Never abstract one-time code
- Never add flexibility that no one asked for
- Never handle errors that cannot possibly occur
- If a senior engineer would think the code is too complex, simplify it
- If 200 lines can be compressed to 50 lines, rewrite it

**Simplicity test:**
After writing code, ask: "Would a senior engineer think this is too complex?" If yes, simplify.

**Examples of what NOT to do:**
- Adding error handling for cases that cannot happen
- Creating abstraction layers for code that will only be used once
- Adding configuration options "just in case"
- Writing 200 lines when 50 lines would suffice

### Principle 3: Surgical Modifications

To modify code safely, only touch what needs touching. Every line change must be traceable to the user's original request.

**Rules:**
- Never improve adjacent code, comments, or formatting
- Never refactor things that are not broken
- Always match existing style, even if there is a better way to write it
- When noticing irrelevant dead code, mention it but do not delete it
- Pre-existing dead code should not be deleted unless explicitly asked

**Verification test:**
Can every line of the change be directly traced to the user's original request? If not, remove the unrelated changes.

**Examples:**
- User asks to fix a bug in function A → only modify function A, do not "improve" function B nearby
- User asks to add a feature → match the existing code style, do not "clean up" the surrounding code
- Noticing dead code → mention it to the user, do not delete it unless asked

### Principle 4: Goal-Driven Execution

To execute effectively, define success criteria instead of steps. Focus on "what does done look like" rather than "what are the steps".

**Rules:**
- Instead of "add validation", say "write tests for invalid input and make them pass"
- Instead of "fix bug", say "write a test that reproduces the bug, then make it pass"
- Instead of "refactor X", say "ensure all tests pass before and after the refactoring"

**Why this works:**
LLMs excel at looping until a goal is achieved. When given success criteria, the AI will iterate until the goal is met. When given steps, it often gets lost halfway through.

**Examples:**
| Don't say | Do say |
|-----------|--------|
| Add validation to the input | Write tests for invalid input and make them pass |
| Fix the bug in the login flow | Write a test that reproduces the login bug, then make it pass |
| Refactor the user service | Ensure all tests pass before and after refactoring the user service |

## Application Workflow

When receiving a coding task, follow this mental checklist:

1. **Understand** - Do I fully understand what is being asked? If not, ask questions.
2. **Simplify** - Is there a simpler way to accomplish this? If yes, propose it.
3. **Scope** - What is the minimal change needed? Only touch what is necessary.
4. **Define success** - What does "done" look like? Define the success criteria before starting.

## Integration with Other Skills

This skill can be used alongside any coding-related skill:
- Before using `pdf` skill → think about what PDF operation is actually needed
- Before using `xlsx` skill → think about the simplest spreadsheet structure
- Before using `docx` skill → think about whether a document is even needed

This skill is about the **thinking process**, not the **implementation details**.

## Notes

This skill is inspired by Karpathy's observations about LLM capabilities:
- LLMs are excellent at iterating until a goal is achieved
- LLMs sometimes get lost when given step-by-step instructions
- Giving success criteria works better than giving procedures

The skill name "Think Before Code" emphasizes that thinking must precede action.
