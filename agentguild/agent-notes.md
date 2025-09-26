# Agent Notes

This file enables asynchronous communication with AgentGuild agents. Add quick notes and TODOs here while agents are working - they'll check and process them automatically.

## Pending notes

<!-- Add your notes here, one per line -->


## Processed notes

<!-- Agents will move completed notes here with timestamps -->

---

## How to Use This File

### Adding Notes

1. **Add notes** under the "Pending notes" section above
2. **One note per line** starting with a dash (-)
3. **Mark completion** by either:
   - Adding another note below (continues the list), or
   - Ending the last note with a dot (.) to mark it complete

### When to Use Agent Notes

**Good for agent notes** ✅:
- Minor refactoring suggestions
- Code style adjustments
- Variable/method renaming
- Small improvements that can wait

**Better to interrupt agent directly** ❌:
- Critical bugs or blocking issues
- Major architecture changes
- Urgent modifications needed now

### Example

```markdown
## Pending notes
- Remember to extract those string values as an enum
- Let's rename the field abcDummy to abcFake.

## Processed notes
[2025-01-15 14:30] mosy-system-architect: Remember to extract those string values as an enum
[2025-01-15 14:25] tdd-spring-engineer: Let's rename the field abcDummy to abcFake
```

**Note**: Agents process complete notes only. The last note needs a dot (.) or another note below it to be considered complete.

---

© 2025 Mosy Software Architecture SL. All rights reserved.

Licensed to AgentGuild customers for internal use only. Distribution, copying, or derivative works prohibited without written permission. Contact: legal@mosy.tech