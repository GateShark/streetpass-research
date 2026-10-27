# cecd:s service calls

We use `cecd:s` (granted to an installed CIA via its RSF, as NetPass does), falling back to `cecd:u` for .3dsx.

| Call | Header | What it does |
|---|---|---|
| Open | 0x000100C2 | Open a path in a box (pid, path, flags) |
| Write | 0x00050042 | Write to the opened path |
| ReadMessage | 0x00030104 | Read one inbox/outbox message by 8-byte ID |
| WriteMessageWithHMAC | 0x00070106 | Write a message; CECD signs it with the box HMAC key |
| Delete | 0x00080102 | Delete a message or path |
| SetData (opt 5) | 0x000900C2 | Commit CECD's save data to storage |
| Start | 0x000B0040 | Resume CECD with a command |
| Stop | 0x000C0040 | Pause CECD with a command |
| GetCecdState | 0x000E0000 | Current daemon state |
| OpenAndWrite | 0x00110104 | Open + write a path in one call |
| OpenAndRead | 0x00120104 | Open + read a path in one call |

## States (GetCecdState)
- **1 = idle**: safe to write.
- **4 = searching / busy**: StreetPass activity in progress. Don't write.

## Busy error
`0xC8A10BF0` (InvalidState / Busy) is returned when you call a write while CECD is busy. Treat it as "retry later". Don't auto-Stop CECD to force it; just check again on a later poll.

## Commit
Writes aren't durable until committed. SetData option 5 commits the save data; it also returns busy if CECD isn't idle, so only commit while idle.
