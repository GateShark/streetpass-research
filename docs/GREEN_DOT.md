# The HOME Menu green dot

## The flags
MBoxInfo bytes **0x40-0x43** are per-box flags. The first two are **unread** and **new**; when set, the HOME Menu shows the green notification dot for the app.

## Why ours got stuck
Our clear routine only ran from a debug button path. Normal StreetPass receipts never called it, so the dot stayed until someone pressed the debug button.

## Safe fix
1. **Re-arm on new mail**: when the inbox count changes, mark "clear needed".
2. **Only when idle**: on each main-loop poll, check GetCecdState == 1 first. Never wait for idle inside the UI loop (that froze our UI for up to ~15 s).
3. **Clear, verify, commit**: write 0 to the flags in your own MBoxInfo, re-read to confirm, then commit with SetData opt 5.
4. **Retry until success**: if busy or the verify fails, leave "clear needed" set and try on the next poll.

Only ever touch your own box.
