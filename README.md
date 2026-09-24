# More Map Variations (MMV) + Seamless Co-op for Elden Ring: Nightreign

A working **ModEngine 3 (me3)** profile that runs **More Map Variations (MMV)** together with **Seamless Co-op** in *Elden Ring: Nightreign* — so you can play modded runs with a full group of up to **6 players**, **no Server Redirector needed**.

If you've hit the dreaded `Method "SignIn" is not available` crash, this repo is the fix.

---

## Why this exists

Vanilla Nightreign caps co-op at **3 players (trio)**. To play with 4–6 people you need **Seamless Co-op**, which raises the cap.

The catch is that MMV ships with **two mutually exclusive multiplayer systems**, and it's easy to load the wrong one (or both) and crash:

| System | File | Players | Use it? |
|---|---|---|---|
| **Server Redirector** (Church Guard) | `cl_server_redirector.dll` | trio / duo | ❌ Not compatible with Seamless. Do **not** use for a big group. |
| **Seamless Co-op** (Yui) | `nrsc.dll` | up to 6 | ✅ This is the one you want. |

**Pick one.** For a group of 4–6, use **Seamless** and make sure the Server Redirector is **not** loaded. Loading both at once = crashes.

---

## The fix, in one line

Load `nrsc.dll` as an **me3 native with `load_early = true`**, and launch through the **`.me3` profile** — *not* through `nrsc_launcher.exe`.

That's it. The rest of this README explains why and how.

---

## Requirements

- **Elden Ring: Nightreign** (owned/installed via Steam)
- **me3** — version **≥ v0.6.0** (older versions won't auto-apply Seamless's init hook). Install it so that **double-clicking a `.me3` file launches the game with that profile.**
- **More Map Variations** — tested on **2.1.8-hotfix3**
- **Seamless Co-op for Nightreign** — tested on **1.1.3** ([Nexus page](https://www.nexusmods.com/eldenringnightreign/mods/3))
- *(Recommended for 4+ players)* **Nightreign 6 Player Fixes** (by Umgak, on GitHub) so boss/event scripts behave correctly for players 4, 5, and 6.

> ⚠️ **Everyone in the group must run identical setups**: same MMV version, same Seamless version, and the same `MMV_Seamless.me3` profile. Mismatches produce errors like *"host is not using seamless co-op"* or *"host version … does not match."*

---

## Folder layout

Your MMV folder should look like this:

```
More Map Variations 2.1.8-hotfix3\
├─ MMV_Seamless.me3          ← the profile you RUN (from this repo)
├─ MMV2.1.8-hf3.me3          ← stock profile (loads Server Redirector) — do NOT run
└─ mod\
   ├─ (MMV assets: chr, parts, map, event, script, regulation.bin, …)
   ├─ SeamlessCoop\          ← must contain nrsc.dll + nrsc_settings.ini
   ├─ ServerRedirector\      ← contains cl_server_redirector.dll (unused here)
   └─ nrsc_launcher.exe      ← do NOT use (bypasses MMV, launches vanilla)
```

---

## Setup

1. **Update me3** to the latest version (≥ v0.6.0) on **every** machine.
2. **Install MMV** and drop the `MMV_Seamless.me3` from this repo into the MMV root folder (next to the `mod\` folder).
3. Confirm **`nrsc.dll`** *and* **`nrsc_settings.ini`** are both inside `mod\SeamlessCoop\`.
4. *(Strongly recommended)* **Move the whole MMV folder out of OneDrive** to something like `C:\Games\MMV\`. OneDrive can lock or sync files mid-launch and cause crashes/corruption.
5. **Launch by double-clicking `MMV_Seamless.me3`.**

**Do NOT launch with any of these** — they bypass MMV or load the wrong multiplayer system:
- ❌ `nrsc_launcher.exe`
- ❌ `NightreignCustomServerLauncher.exe`
- ❌ the stock `MMV2.1.8-hf3.me3`

---

## The working profile (`MMV_Seamless.me3`)

```toml
profileVersion = "v1"
start_online = false

[[supports]]
game = "nightreign"

[[packages]]
id = "MMV"
path = 'mod'

[[natives]]
path = 'mod/SeamlessCoop/nrsc.dll'
load_early = true
```

Notes:
- Paths are **relative to the `.me3` file's location**.
- `load_early = true` is the critical line — it lets Seamless hook the game's `SignIn` function in time.
- `start_online = false` is correct for Seamless. (Only the Server Redirector needs `true`.) If co-op connection fails, you can try flipping it to `true` as a test.
- `game = "nightreign"` also accepts `"nightrein"` and `"nr"`.

---

## Troubleshooting

### ❌ `Method "SignIn" is not available … make sure seamless co-op is loaded early` (`auto_control_api.cpp:20`)
Seamless loaded too late to hook the game. **Add `load_early = true`** to the `nrsc.dll` native block (see profile above). This is the main fix.

### ❌ Game launches vanilla with no map variations
You launched via `nrsc_launcher.exe`. It finds `nightreign.exe` through Steam and runs it directly, skipping MMV entirely. **Always launch via the `.me3` profile instead.**

### ❌ Profile launches but Seamless never activates
Your me3 is too old. Update to **≥ v0.6.0** so me3 auto-applies Seamless's `modengine_ext_init`.

### ❌ Random crashes on launch / corrupted files
- Make sure the **Server Redirector is not also loaded** — only the Seamless native belongs in the profile.
- Move the folder **out of OneDrive**.

### ❌ "host is not using seamless co-op" / "host version … does not match"
Someone in the group has a different setup. Everyone must run the **same MMV version, same Seamless version, and the same profile**.

---

## Save safety

Seamless uses a **separate save file** (extension `.co2`), which keeps your modded runs off your retail save and reduces ban risk on official servers. **Do not reuse the modded save on vanilla online play.**

---

## Credits

- **More Map Variations (MMV)** — MMV team
- **Seamless Co-op for Nightreign** (`nrsc.dll`) — Yui
- **me3 (ModEngine 3)** — ModEngine team
- **Nightreign 6 Player Fixes** — Umgak
- Profile + writeup by **eepycloud**

*This project only provides the me3 profile that makes MMV and Seamless work together. Install MMV, Seamless, and me3 from their official sources.*
