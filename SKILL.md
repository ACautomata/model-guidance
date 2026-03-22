---
name: model-guidance
description: Use when starting any session or new conversation. Must be invoked before the first response and before any other action, including clarifying questions.
---

# Model Guidance

## ⚠️ MANDATORY: Session Initialization Hook

**This skill MUST be invoked at the very start of every session before ANY other action.**

Trigger conditions (ANY of these means invoke immediately):
- Session just started (first message received)
- New conversation begins
- User mentions "model guidance" or similar

Do NOT proceed with any other task until model guidance is loaded.

---

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

> ⚠️ **Loading is INCOMPLETE until all 5 steps are executed. Invoking this skill ≠ loading complete.**
> Do NOT respond to the user's task until Step 5 is finished.

```
1. Parse announcement → 2. Match pattern → 3. Load file → 4. Integrate → 5. Apply + confirm
                                                ↑
                                    REQUIRES Read tool call
```

### Step-by-Step

1. **Parse announcement**: Look for `[SYSTEM: CURRENT_MODEL_ANNOUNCEMENT - You are {MODEL_NAME} ({MODEL_ID})]`
2. **Match pattern**: Check MODEL_NAME and MODEL_ID against routing table
3. **Load file**: **Use the `Read` tool** to load the matched file from `references/`. Skipping this tool call = incomplete loading.
4. **Integrate**: Fuse the loaded guidance with ALL existing requirements (CLAUDE.md, user instructions, project rules). See Integration Principle below.
5. **Apply + confirm**: Internalize the unified behavior profile. Then output internally: `[model-guidance: {MODEL_FAMILY} loaded, integrated]` before proceeding.

---

## ⚠️ Integration Principle — NO Binary Choice

> **CRITICAL: Loading the guidance file does NOT replace or override existing requirements. It ADDS to and ENRICHES them.**

After loading the guidance file, you MUST perform a **synthesis step**:

### What Integration Means

| Scenario | WRONG (binary) | CORRECT (integrated) |
|----------|----------------|----------------------|
| Guidance says "be concise"; CLAUDE.md says "add type hints" | Pick one style | Be concise AND add type hints |
| Guidance prefers certain reasoning style; user gave explicit instructions | Follow guidance OR follow user | Let guidance shape HOW you follow user instructions |
| Guidance defines communication tone; project has coding standards | Apply tone OR apply standards | Use guidance tone while strictly following coding standards |

### Integration Algorithm

```
existing_context = {CLAUDE.md rules, user instructions, project conventions, session history}
model_guidance   = loaded from references/<model-family>.md

final_behavior   = merge(existing_context, model_guidance)
                   # Where conflicts arise: user/project rules WIN
                   # Where guidance enriches: apply guidance on top
                   # Never discard either source
```

### Conflict Resolution Priority

1. **Explicit user instruction** (highest priority — always respected)
2. **Project rules** (CLAUDE.md, AGENTS.md)
3. **Model guidance** (enriches, does not override)
4. **Default model behavior** (lowest priority)

### Anti-Patterns to Avoid

- ❌ "The guidance says X, so I'll ignore the project rule Y"
- ❌ "There's a conflict, so I'll only follow the guidance"
- ❌ "I'll switch to guidance mode and ignore prior instructions"
- ✅ "The guidance adds insight Z — I'll apply it while keeping rule Y intact"

---

## Conditional Branches

| Condition | Steps to Complete |
|-----------|-------------------|
| Announcement found + pattern matched | All 5 steps |
| Announcement found + no match | Steps 1-2, skip 3, then steps 4-5 with defaults |
| No announcement | Skip steps 1-3; execute steps 4-5 with existing context only |
| Multiple patterns match | Use first match in routing table order, then all 5 steps |

> **No announcement does NOT mean skip the skill.** Steps 4-5 (integrate + apply existing context) always run.

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
| No announcement detected | Skip steps 1-3; still execute steps 4-5 with existing context |
| Unknown model family | Proceed without guidance; log for future mapping |
| File not found | Verify `references/` directory exists; check file names |
| Pattern matches wrong family | Check routing table order; patterns are matched top-to-bottom |

## ✅ Completion Gate

> **Do NOT proceed to the user's task until all items are checked.**

- [ ] Step 3 Read tool call executed (or confirmed skipped due to no match)
- [ ] Guidance integrated with (not replacing) existing requirements
- [ ] No existing CLAUDE.md / user instructions discarded
- [ ] Conflict resolution followed correct priority order
- [ ] Output confirmation: `[model-guidance: {MODEL_FAMILY} loaded]`