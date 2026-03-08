---
name: model-guidance
description: "Automatically loads at the start of every conversation session. Provides model-specific guidance files containing thinking patterns, behavior guidelines, and persona instructions for various AI models. Merge instructions from previous conversation context with {model}-guidance loaded instructions, satisfying both sets of requirements."
---

# Model Guidance

## Overview

This skill provides model-specific guidance files for various AI models. Each guidance file contains the specific thinking patterns, behavior guidelines, and persona instructions for a particular AI model.

## Available Model Guidance

Select the appropriate guidance file based on the AI model you're working with:

- **[claude-guidance.md](references/claude-guidance.md)** - Anthropic Claude specific guidance
- **[chatgpt-guidance.md](references/chatgpt-guidance.md)** - OpenAI ChatGPT specific guidance
- **[deepseek-guidance.md](references/deepseek-guidance.md)** - DeepSeek specific guidance
- **[glm-guidance.md](references/glm-guidance.md)** - Zhipu AI GLM specific guidance
- **[minimax-guidance.md](references/minimax-guidance.md)** - MiniMax specific guidance
- **[kimi-guidance.md](references/kimi-guidance.md)** - Moonshoot KIMI specific guidance
- **[gemini-guidance.md](references/gemini-guid.md)** - Google Gemini (deprecated)

## Workflow

1. **Identify the model**: Determine which AI model's guidance to load
2. **Read the guidance**: Open `references/{model}-guidance.md`
3. **Merge instructions**: Combine instructions from previous conversation context (CLAUDE.md, system prompts, user preferences) with {model}-guidance loaded instructions
4. **Apply the guidance**: Follow the merged instructions, ensuring both sets of requirements are satisfied
