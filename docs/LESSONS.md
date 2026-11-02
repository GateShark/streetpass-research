# Lessons

Each point below is backed by our app's source or logs from testing on our own hardware.

## The HOME Menu green dot
- **The dot comes from MBoxInfo flags.** The green dot for our box (CEC ID `0FF5A700`) comes from MBoxInfo bytes `0x40`–`0x43` (the unread and new flags). Setting those four bytes to `00000000` and leaving every other MBoxInfo byte unchanged clears the dot. Read the data back afterward to confirm the write. See [GREEN_DOT.md](GREEN_DOT.md).
- **Run the clear automatically.** At first our clear only ran from a manual debug action, so the dot stayed lit. The working fix runs the clear from the normal poll loop.
- **Clear only when CECD is idle, and re-arm it on new mail.** The poll loop starts a clear only when `GetCecdState` reports state 1 (idle). It never waits on the UI thread. If the clear fails or CECD is busy, the poll retries it next time. New inbox mail re-arms the clear until one succeeds. If you only clear once, mail that arrives while CECD is busy leaves the dot stuck.

## CECD states and busy handling
- **States:** 1 is idle. 4 means CECD is searching or busy. While CECD searches it locks the box files, and box access returns `C8A10BF0`. Treat that as "busy, try again later", not as a failure.
- **Writes and commits happen only when CECD is idle.** Check that CECD is idle again right before each write and before the commit. Our clear routine waits up to about 1 s for idle and skips the write if CECD is still busy.
- **Commit your changes.** Box changes are saved only after SetData option 5 runs while CECD is idle.
- **Don't Stop the service just to get access.** Our normal paths never call Stop. The one exception is a deliberate user action, and that path always waits for idle afterward.

## Packaging
- **The CIA micro version is 4 bits.** The micro field only holds 0–15. Plan your version numbers around that limit.
