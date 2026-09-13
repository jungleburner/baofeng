# Baofeng UV-28Plus — Australian UHF CB — Session Notes

**Status: COMPLETE.** 80 CB channels programmed to Zone 01, slots 1–80. TX confirmed on 477 MHz and verified on-air via repeater (§6a). A/B side switching resolved (§7). Only remaining item is an optional stock-whip comparison against the Abbree (§6).

---

## 1. Hardware / Identification

| Item | Value |
|---|---|
| Radio | Baofeng UV-25 Plus (badged), **CHIRP driver = `UV-28Plus`** |
| CHIRP version | next-20260904 |
| CPS | BF-UVPROs CPS, Model dropdown `UV18PRO`, internal model string `UV25PRO` |
| Cable | 2-pin Kenwood, CH340 chipset, COM3 |
| Display | Dual (A top / B bottom), `MAIN` tag marks active side |
| Memory | 1000 channels, 10 zones ("Banks") × 100 |
| GPS | Never confirmed. `gpsSw`/`gpsMode` fields exist in firmware but that doesn't prove hardware. |
| TX at 477 MHz | **CONFIRMED WORKING** — no refusal, despite manual specifying 420–450 MHz |
| RX ranges | **136–174 / 220–260 / 350–400 / 400–520 MHz** (read off the CPS window — authoritative) |
| TX ranges (spec) | 144–148 / 420–450 MHz per manual — radio transmits at 477 regardless |

---

## 2. Key mapping (established by testing)

| Key | Short press | Long press |
|---|---|---|
| Green (house) | Open menu | **VFO ↔ MR toggle** |
| ▲ / ▼ | Change channel | — |
| Red | **A/B switch — toggles MAIN between top and bottom lines** | Exit/back |
| SK1 (+ side key) | **FM radio** | — |
| SK2 (− side key) | nothing | Alarm |
| Black (near antenna) | nothing | Alarm |
| `*` | Beeps only | Keypad lock |
| `#` | — | Scan start/stop |
| `0` | — | NOAA weather (US only — useless in AU) |

**Menu 3 → 26 "PRESS SK1"** offers only: FM RADIO / SCAN / SEARCH / VOX. No SK2 menu entry exists. **No A/B option available.**

---

## 3. Menu reference

- `MENU` → `3` → Radio Setting
- **Item 25 = MENU EXIT TIME** ← set to 60, default is far too short and closes menus mid-scroll
- Item 26 = PRESS SK1
- Menu 5 = per-channel settings (TX power, Scan Add etc.) — per channel, so use CHIRP for bulk changes
- Menu 2 → 2 = Scan resume mode (Carrier / Time / Search) — **Carrier** is right for CB
- Menu 3 → 2 = Squelch (OFF, 1–5)
- Menu 3 → 24 = **TAIL** (squelch tail elimination) — **leave OFF for CB.** It needs CTCSS/DCS to work, and AU UHF CB uses plain carrier squelch, so it has nothing to act on. Same applies to the related `tailClear` / `rptTailClear` / `rptTailDet` firmware fields.

---

## 4. Channel file

**Verified contents:**
- 80 rows, Locations **1–80** (CB 01R in radio CH-001)
- Ch 1–40: 476.4250 + (N−1)×0.025
- Ch 41–80: 476.4375 + (N−41)×0.025
- Repeater channels 1–8, 41–48: `Duplex=+`, `Offset=0.750000`
- Skip `S` on ch 22/23 (data-only, voice prohibited)
- TStep 12.50 (25.00 on 22/23)
- Mode NFM throughout
- Power `10W` in CSV / displays as `High` in img tab

### CSV format gotchas (cost several failed imports)
- Power **must** be a wattage string like `10W` — `High` and blank both rejected by generic_csv
- CSV tab in CHIRP is **0-indexed**; the .img tab is **1-indexed**. Always import into the .img tab and verify row numbering there before upload.
- After import, check for a **duplicate leftover row** past the last channel (row 81 held a second CB 80)

---

## 5. Band plan reference

| Channels | Role |
|---|---|
| 1–8 | Repeater output (input = 31–38, +0.750 MHz) |
| 41–48 | Repeater output (input = 71–78, +0.750 MHz) |
| 31–38, 71–78 | Repeater inputs — don't use simplex |
| **5, 35** | **Emergency only** |
| **22, 23** | **Data/telemetry only — voice prohibited** |
| 10 | 4WD / clubs / national parks |
| 11 | Call channel |
| 18 | Caravan / RV convoy |
| 29 | Pacific Hwy (NSW) / Bruce Hwy (QLD) |
| 30 | UHF CB broadcasts |
| 40 | National road/highway channel |

Range 476.4250–477.4125 MHz. 12.5 kHz spacing except 22/23 at 25 kHz.

---

## 6. Antenna

- **Stock antenna** is cut for 400–450 MHz — off-band for 477.
- **Abbree 48cm tactical**, SMA-Female — **in use, proven on-air** (see §6a). Rated 400–470, so nominally ~7 MHz below CB, but roughly half-wave at 477 and works fine in practice.
- **Wiltronics TXAN3070** ($19.95) — investigated and **rejected**: photo shows a large brass base-mount thread (~5/16"-26), not SMA. It's a mobile antenna whip, won't fit a handheld.
- Connector needed: **SMA-Female** (radio has the male pin). Most Nagoya/Baofeng antennas are SMA-Male and will NOT fit.
- Best test: SWR meter at 477 MHz. Under 2:1 good, over 3:1 stresses the PA.

**Power caution:** AU UHF CB handheld limit is 5W EIRP; this radio does ~10W on High. Currently set to High per user request. High power into an off-resonant antenna is the main risk to the finals.

---

## 6a. On-air results — CONFIRMED

- **Ch 42, via repeater, using the Abbree 48cm** — radio check returned successfully.
- Proves the whole chain: correct frequency, correct +0.750 MHz offset, enough radiated power at 477 MHz to key a repeater input, and intelligible audio.
- The Abbree works in practice despite being rated only to 470 MHz.
- **Still worth doing:** same repeater with the stock whip, to see whether the Abbree is genuinely better or the repeater is simply close enough that either gets in.

---

## 7. A/B side switching — RESOLVED

**Red key, short press, in standby** toggles `MAIN` between the top (A) and bottom (B) lines.

Combined with green long-press (VFO ↔ MR), this gives full independent control:
1. Red to select the side you want
2. Green long-press to set that side's mode
3. ▲/▼ to change that side's channel

Example: A on VFO for band sweeping, B on the CB channel list for monitoring.

**Why it wasn't findable in the menus:** the firmware's 43 named settings fields include `chAWorkmode` and `chBWorkmode` (A/B hold independent VFO/MR modes) but contain **no field for which side is MAIN** — that's runtime state, not stored config. It was always going to be a key action. SK1 only offers FM/SCAN/SEARCH/VOX, and no SK2 menu entry exists, so red was the remaining candidate.

---

## 7a. VFO save-to-memory & display type

### Saving a VFO frequency to memory
Green MENU → Bank → pick zone → save. The Bank prompt only lists zones that already exist, which is why **Zone 1 was the only option** (zones 2–10 are empty).

**Where it lands:** most likely the first free slot — **CH-081**, since 1–80 are occupied. Some firmware instead overwrites the last-selected channel, so check both CH-081 and whatever channel was active beforehand.

To confirm precisely: read the radio into CHIRP and inspect rows 81+. Delete or relocate from there.

### Reverse (listen on a repeater's input)
**Short press `*`** on a duplex channel swaps TX and RX — on ch42 you'd listen on 477.2125 and transmit on 476.4625. Used to check whether the other station is in direct simplex range, bypassing the repeater.

On a simplex channel it only beeps, since there's nothing to swap. (This is why the earlier `*` test appeared to do nothing — it was tried on a simplex channel.)

### Showing frequency instead of name in MR mode
Stored per side as `chADisType` / `chBDisType`, so A and B can differ.

- On the radio: **Menu 5**, look for `CH-MDF` or `DISPLAY` — options NAME / FREQ / CH-number
- Or set both sides in CHIRP's Settings tab

---

## 7b. VFO scanning (VHF and UHF)

| Setting | Where |
|---|---|
| Step | Menu 3 → item 1 |
| VFO scan range | Menu 2 → item 1 — start and end as six digits (`136174` = 136–174 MHz) |

**Step does not affect memory-channel scanning.** Each channel stores its own explicit frequency, so the 80 CB channels scan identically whatever the step is. Step only matters for VFO tuning and VFO sweeping — **no need to change it back after switching bands.**

**Step for VHF:** use 12.5 or 25 kHz to match Australian allocations. 2.5 kHz sweeps ten times slower and stops on nothing useful.

Switching between VHF and UHF sweeping means changing **two** settings — step and scan range. The range is what actually constrains the sweep.

VHF band to explore: **136–174 MHz**. Scanning is receive-only and legal anywhere; transmitting outside licensed bands is not.

---

## 8. Image structure (reverse-engineered, for reference)

### CHIRP `.img` (33853 bytes)
| Offset | Contents |
|---|---|
| 0x0000–0x0A00 | 80 channel records, 32 bytes each, name at +0x14 |
| 0x8000–0x801F | VFO A state |
| 0x8020–0x803F | VFO B state |
| 0x8040–0x807F | Global settings (64 bytes, meanings not decoded) |
| 0x8280 | Zone names, 16 bytes each |
| 0x838D | CHIRP metadata, base64 JSON |

### CPS `.dat` (88384 bytes)
- .NET BinaryFormatter (MS-NRBF) serialized object graph, app `BF_H802_CPS`
- Fully parses: 1243 objects, IDs 1–1804, no duplicates
- `Channel` class, 14 fields: id, rxFreq, strRxCtsDcs, txFreq, strTxCtsDcs, busyLock, txPower, bandwide, scanAdd, sqMode, pttid, signalGroup, fhss, name
- Frequencies stored as 9-char strings (`"476.42500"`); rx/tx separate, so no duplex/offset field
- **Encodings confirmed against display:** txPower `0`=High, bandwide `0`=Wide/`1`=Narrow, scanAdd `1`=Add
- `txPower=2` appears **invalid** — caused CPS to abort rendering mid-row
- Object IDs must stay in the file's normal range; IDs around 10^6 caused a hard "读取失败" (read failed)

**Conclusion on hand-editing CPS files:** possible but fragile. CHIRP is the better path now that it works.

---

## 9. Troubleshooting crib

**"No response from radio" but PROGRAM flashes on screen** → data reaches radio, reply doesn't. Reseat the 2-pin plug HARD.

**"No response" with no PROGRAM** → in order: close CPS completely (it holds COM3 exclusively); radio to standby (won't handshake from a menu/scan/NOAA); reseat plug; restart CHIRP; try another USB port and re-check the COM number.

**Before any upload:** full battery, don't touch the cable, disable PC sleep. Read back afterwards to confirm.
