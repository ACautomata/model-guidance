---
name: model-guidance
description: Detects current model family from system announcement and loads model-specific thinking patterns and behavior guidelines. Invoke at session start via Skill tool before first user interaction. Maps model identity to appropriate guidance file automatically.
---

# Model Guidance

## Purpose

Automatically detect and load model-specific behavior patterns at session initialization. Routes model identity to appropriate guidance file containing thinking styles, behavior preferences, and interaction patterns optimized for that model family.

## When to Use

- **Session start** — before first user interaction
- **Model switch** — when model identity changes mid-session
- **Explicit request** — user asks to load model guidance

## When NOT to Use

- Mid-conversation queries about model capabilities
- Model comparison or debugging questions
- General AI questions unrelated to behavior patterns

## Default Workflow

```
1. Parse announcement → 2. Match pattern → 3. Load file → 4. Apply guidance
```

### Step-by-Step

1. **Parse announcement**: Look for `[SYSTEM: CURRENT_MODEL_ANNOUNCEMENT - You are {MODEL_NAME} ({MODEL_ID})]`
2. **Match pattern**: Check MODEL_NAME and MODEL_ID against routing table
3. **Load file**: Read corresponding file from `references/` directory
4. **Apply**: Internalize patterns for all subsequent thinking and responses

## Conditional Branches

| Condition | Action |
|-----------|--------|
| Announcement found + pattern matched | Load guidance file |
| Announcement found + no match | Proceed without guidance; consider logging for future |
| No announcement | Skip gracefully; use default behavior |
| Multiple patterns match | Use first match in routing table order |

## Model Family Routing

| Detection Pattern | Guidance File |
|-------------------|---------------|
| `GLM-`, `glm-`, `bailian-coding-plan/glm` | `references/glm-guidance.md` |
| `Claude`, `claude-`, `anthropic` | `references/claude-guidance.md` |
| `GPT-`, `gpt-`, `chatgpt`, `openai` | `references/chatgpt-guidance.md` |
| `DeepSeek`, `deepseek` | `references/deepseek-guidance.md` |
| `MiniMax`, `minimax` | `references/minimax-guidance.md` |
| `Kimi`, `kimi`, `moonshot` | `references/kimi-guidance.md` |
| `Gemini`, `gemini`, `google` | `references/gemini-guidance.md` |

## Examples

### Example 1: GLM Detection

```
Input: [SYSTEM: CURRENT_MODEL_ANNOUNCEMENT - You are GLM-5 (bailian-coding-plan/glm-5)]
Match: "GLM-" and "bailian-coding-plan/glm" → GLM family
Load: references/glm-guidance.md
```

### Example 2: Claude Detection

```
Input: [SYSTEM: CURRENT_MODEL_ANNOUNCEMENT - You are Claude (claude-3-opus)]
Match: "Claude" and "claude-" → Claude family
Load: references/claude-guidance.md
```

### Example 3: Unknown Model

```
Input: [SYSTEM: CURRENT_MODEL_ANNOUNCEMENT - You are NewModel (unknown/model-1)]
Match: No pattern matches
Action: Proceed without guidance
```

## File Map

```
references/
├── glm-guidance.md      # GLM family patterns
├── claude-guidance.md   # Claude family patterns
├── chatgpt-guidance.md  # GPT family patterns
├── deepseek-guidance.md # DeepSeek patterns
├── minimax-guidance.md  # MiniMax patterns
├── kimi-guidance.md     # Kimi patterns
└── gemini-guidance.md   # Gemini patterns
```

## Troubleshooting

| Issue | Resolution |
|-------|------------|
| No announcement detected | Skip gracefully; use default behavior |
| Unknown model family | Proceed without guidance; log for future mapping |
| File not found | Verify `references/` directory exists; check file names |
| Pattern matches wrong family | Check routing table order; patterns are matched top-to-bottom |

## Validation Checklist

After loading:
- [ ] Model family correctly identified from announcement
- [ ] Appropriate guidance file loaded without errors
- [ ] Thinking patterns align with guidance content