# eez-rigol-mho98

EEZ Studio extension for the **Rigol MHO98** — 1 GHz, 4-channel, 4 GSa/s
oscilloscope with a built-in dual-channel 100 MHz AWG.

Like [eez-keysight-34465a](https://github.com/ksstech/eez-keysight-34465a)
and unlike [eez-ea-ps2k](https://github.com/ksstech/eez-ea-ps2k), this
instrument speaks **native SCPI** directly — no custom bridge process, EEZ
Studio connects straight to the instrument.

## Connection

- **Ethernet (LXI), recommended:** port `5555`. Enter the instrument's IP
  address when adding it in EEZ Studio — default lab address `192.168.1.51`.
- **USB-TMC:** `idVendor 0x1ab1`, `idProduct 0x0517`.

## Structure

| Path | Purpose |
|---|---|
| `package.json` | Extension metadata + all 28 shortcuts |
| `rigol_mho98.idf` | EEZ Studio instrument definition |
| `rigol_mho98.sdl` | SCPI command/response definitions |
| `image.png` | Extension icon |

Built as a zip and published via [GitHub Releases](https://github.com/ksstech/eez-rigol-mho98/releases) — not committed to the repo.

## Functionality — shortcuts

**Acquisition control** (toolbar, `scpi-commands`): `Run` `Stop` `Single`
`Auto` `Force`.

**JavaScript dialogs/tools** (use the shared
[`qts()`](https://github.com/ksstech/eez/blob/main/docs/qts-helper.md) helper,
and the [live-toast pattern](https://github.com/ksstech/eez/blob/main/docs/eez-live-toast-pattern.md)
for continuous readouts):
- `Capture` — pull all displayed channels to charts. Uses `RIGOL_BYTE`
  waveform format with the standard preamble conversion
  `V = (raw - YORigin - YREFerence) × YINCrement`.
- `Live Meas` — continuous Vpp/Vrms/Freq/Period/Vmax/Vmin readout (persistent
  toast, click Stop to end — same pattern as eez-ea-ps2k's `Live` shortcut).
- `Vertical` — per-channel: on/off, coupling, impedance, probe, scale,
  offset, unit, bandwidth limit, invert, fine adjust.
- `Trigger` — source, slope, level, sweep, coupling, holdoff, noise reject.
- `Horizontal` — timebase scale/position/mode, acquisition type, averages,
  memory depth, expand reference; also reports live sample rate.
- `Measure` — all measurement items, grouped Vertical (voltage) / Horizontal
  (time) / Other (count, delay, phase), with an optional on-screen statistic.
- `AWG` — configure a generator channel.

**Quick toggles** (toolbar-hidden, `scpi-commands`): `CH1–4 On/Off`,
`AWG1/2 On/Off`, `Screenshot`, `Clear Errors`, `Reset`.

**Utility:** `Diag` (JavaScript, full diagnostic dump).

## Using it without EEZ Studio

Native SCPI — works with any SCPI client already; this repo only adds
convenience shortcuts on top.

**Raw socket (LAN SCPI, port 5555):**
```bash
echo -e "*IDN?\n" | nc 192.168.1.51 5555
# RIGOL TECHNOLOGIES,MHO98,...
```

**Python via PyVISA:**
```python
import pyvisa
rm = pyvisa.ResourceManager()
scope = rm.open_resource("TCPIP0::192.168.1.51::5555::SOCKET")
scope.read_termination = "\n"
scope.write_termination = "\n"

print(scope.query("*IDN?"))
scope.write(":RUN")
print(scope.query(":MEASure:VPP? CHANnel1"))
```

## License

No LICENSE file is currently set — add one if this is meant to be reused
under specific terms; until then, standard GitHub default (all rights
reserved) applies to original content here.
