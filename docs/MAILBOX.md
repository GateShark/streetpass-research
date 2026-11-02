# Mailbox layout

Each StreetPass app has a box keyed by its CEC ID. Paths used with Open/OpenAndRead:

| # | Path |
|---|---|
| 1 | MBoxList: list of registered boxes, magic **0x6868** |
| 2 | MBoxInfo: box header, magic **0x6363** |
| 3 | InboxInfo: BoxInfo, magic **0x6262** |
| 4 | OutboxInfo: BoxInfo, magic **0x6262** |
| 5 | OutboxIndex: OBIndex, magic **0x6767** |
| 6 | Inbox message |
| 7 | Outbox message |

Always check the magic after a read; a mismatch means the box is missing or the read failed.

## Registration
1. Read MBoxList and check whether your ID is already present (we cap it at 12 boxes, the same limit NetPass uses). Rewrite MBoxList with every other entry preserved.
2. If not, create MBoxInfo (with your HMAC key and timestamps), empty Inbox/Outbox BoxInfo and an OBIndex.
3. Only do this while CECD is idle, then commit.

## Outbox rebuild
To replace your outgoing data: delete the old outbox message, write a new one with WriteMessageWithHMAC. Pass an all-zero ID buffer; CECD only generates the ID and HMAC trailer in that case, and make sure OutboxInfo and OBIndex agree with it. Keep the outbox max size well above one message (we use 0x8000). Idle only; on busy, retry on a later poll.

## HMAC
Messages carry an HMAC-SHA256 trailer over the body. The 32-byte key lives in MBoxInfo; we use one fixed key per app, the same on every console, so two units running the same app can verify each other's messages. Verify received bodies before trusting them.

## last_accessed and commit
MBoxInfo holds `last_accessed` and `last_received` timestamps. Changes to MBoxInfo (including flags) only persist after a SetData commit; without it they can be lost on reboot.
