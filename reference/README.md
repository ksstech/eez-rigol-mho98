# SCPI command-set reference data (extracted 2026-08-11)

Input data for SDL generation and the MHO900-vs-DHO900 platform diff —
per the plan in
[ksstech/eez: docs/rigol-scope-support-plan.md](https://github.com/ksstech/eez/blob/master/docs/rigol-scope-support-plan.md).

## Provenance

Extracted from Rigol's official programming guides (PDFs not committed —
Rigol copyright):

- MHO900: https://www.rigol.com/dam/global/downloads/brochures/en/program-guide/oscilloscopes/MHO900-ProgrammingGuide.pdf (518 pages)
- DHO800/DHO900 (one shared guide): https://download.rigol.com/en/Manual/Digital%20Oscilloscope/DHO900/DHO800900_ProgrammingGuide_EN.pdf (452 pages)

Method: pypdf full-text extraction, then command-heading regex
(lines consisting solely of a SCPI path like `:CHANnel<n>:SCALe` or a
`*XXX` common command). Counts are of *distinct command paths*, not
query/set variants.

## Files

| File | Contents |
|---|---|
| `mho900_cmds.txt` | 581 command paths from the MHO900 guide |
| `dho800900_cmds.txt` | 515 command paths from the DHO800/900 guide |
| `mho_only.txt` | 101 paths only in the MHO900 guide |
| `dho_only.txt` | 35 paths only in the DHO800/900 guide |

## Key verified findings

- **480 command paths shared** — one platform, confirmed at
  vendor-document level (previously only inferred from our two
  extensions' 123-entry SDL overlap).
- **The AWG subsystems differ structurally**: DHO900S uses unindexed
  `:SOURce:...` (1 channel); MHO900 uses indexed `:SOURce<n>:...`
  (2 channels), with a larger sub-command set (adds `PERiod`,
  `IMPedance`, `LOAD:ARBitrary`, `PHASe:SYNChronize`,
  `VOLTage:HIGH/LOW`). Any shared script must parameterize the AWG
  command prefix, not just the channel count.
- MHO900-only, real subsystems: `:BODeplot` (AWG option's Bode plot),
  FLEXray and IIS decode buses + their trigger commands, `:HISTogram`.
- DHO-only, real subsystems: `:ACQuire:ULTRa:*` (UltraAcquire),
  `:NAVigate:FRAMe:*`, `*RCL`, `:MEASure:CLEar`.
- Caveat: heading-regex extraction can carry minor artifacts; the SDL
  generation step must parse the guide sections in detail per command
  (syntax/parameters/remarks), treating these lists as the index.
