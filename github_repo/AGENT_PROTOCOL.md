# AGENT_PROTOCOL

## Target: `chat_stream.md` (Active append-only message bus)

### Message Format:
<!-- studio:{"id":"UUID32","role":"agent","name":"YOUR_NAME","time":"ISO8601","reply_to":"TARGET_ID"} -->
### YOUR_NAME

Your response text here.

---

### Strict Execution Rules:
1. APPEND ONLY: Never edit or delete prior messages. Append at EOF after the final `---`.
2. TURN TAKING: Reply ONLY if:
   - Message is from `role: "user"`.
   - Your name is explicitly tagged (e.g. `@Muse`, `@Gemini`).
   - Do NOT reply to other agents automatically (prevent infinite loops).
3. LINKING: `reply_to` MUST match the target message `id`.
4. ENCODING: Output raw UTF-8. Keep metadata comment on a single line.

### Selective Reading & Token Optimization:
1. LINE-RANGE ADDRESSING: Read ONLY targeted line ranges (`StartLine` -> `EndLine`).
2. TAIL-ONLY READ: Creative agents read ONLY the latest scene window at EOF. Do NOT load full history.
3. HIGH-SPEED POLLING: Check for changes every <= 250ms (0.25s) using file-size/mtime stat check.

### Specialized Agent Roles:
- **CREATIVE AGENTS (Gemini / Codex)**: Focus strictly on narrative, dialogue, and code. Read only active `chat_stream.md` tail.
- **ARCHIVE AGENT (Muse / Dedicated PC Agent)**:
  - Role: Maintains project organization in the background 24/7.
  - Duties:
    1. Every finished session/scene, archive completed messages into `archive/scene_XX.md`.
    2. Maintain `index.json` (maps character profiles, chapters, and line ranges).
    3. Update `state.json` (compressed 3-line recap of canon facts).
    4. Keep `chat_stream.md` clean, lightweight, and bounded (< 30 messages).
