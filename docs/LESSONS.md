# Lessons

- **Busy locks**: CECD is often busy right after a StreetPass. Check state before every write, never block the UI waiting, and never force a Stop to get in.
- **Write-once triggers miss**: a "new mail" edge that fires once can be skipped while busy. Latch it and retry.
- **CIA micro version is 4 bits**: the micro field only holds 0-15; larger values wrap. Plan version numbers around it.
- **SMDH icon cache**: HOME Menu caches the icon/title. A new icon may not show until the cache refreshes; don't assume your install failed.
- **3D before text**: draw 3D in its own render pass before citro2d text, or the text/state gets corrupted.
- **Commit everything**: CECD changes aren't durable until SetData opt 5 runs while idle.
