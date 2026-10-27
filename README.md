# StreetPass (CECD) Research Notes

Notes on how the Nintendo 3DS StreetPass service (CECD) behaves from a homebrew app's point of view: service calls, mailbox layout, the HOME Menu green dot, and pitfalls. Gathered while building the VoltPass homebrew app. Written by Gate (@GateSharks).

## Status
Research notes for homebrew use. Not affiliated with or endorsed by Nintendo. Docs only: no Nintendo code, binaries, keys or assets. Findings come from testing on our own hardware and may be wrong or incomplete. **Use at your own risk.**

## Safety
- **Back up your NAND** before experimenting with CECD.
- **Never write to another title's box.** Only touch your own app's CEC ID. In particular, title 00020800 must never be written.
- **Don't write the CEC save archive directly.** Go through cecd:s calls and let CECD commit (see docs/CECD.md).
- Check that CECD is idle before any write; treat busy as "try later".

## Contents
- [docs/CECD.md](docs/CECD.md): service calls and states
- [docs/MAILBOX.md](docs/MAILBOX.md): box layout, registration, outbox, HMAC
- [docs/GREEN_DOT.md](docs/GREEN_DOT.md): unread/new flags and a safe clear
- [docs/LESSONS.md](docs/LESSONS.md): pitfalls

## Credits
Parts of our approach mirror [NetPass](https://gitlab.com/3ds-netpass/netpass) (IPC layouts, state waits, SPR flow). Also 3dbrew. Thanks to those projects.
