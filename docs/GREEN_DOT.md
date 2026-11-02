# The HOME Menu green dot

## The flags
MBoxInfo bytes **0x40-0x43** are per-box flags (unread, new, and two more we don't know the meaning of). When they're set, the HOME Menu shows the green dot for the app. Clearing all four to `00000000`, and leaving every other MBoxInfo byte as it was, removes the dot.

## Why ours got stuck
Our clear routine only ran from a debug button path. Normal StreetPass receipts never called it, so the dot stayed until someone pressed the debug button.

## Safe fix
1. **Re-arm on new mail**: when the inbox count changes, mark "clear needed".
2. **Only when idle**: on each main-loop poll, check GetCecdState == 1 (idle) first; state 4 means busy. Never wait for idle inside the UI loop.
3. **Clear, verify, commit**: write 0 to all four flag bytes in your own MBoxInfo, re-read to confirm, then commit with SetData opt 5.
4. **Retry until success**: if busy or the verify fails, leave "clear needed" set and try on the next poll.

Only ever touch your own box.
