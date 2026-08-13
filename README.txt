 Aerofly FS 4 Live Weather Injector

A standard-library Python/Tkinter desktop utility for macOS that downloads
METAR weather from Aviation Weather Center, converts it to the known Aerofly
FS 4 `main.mcf` weather fields, and applies changes safely.

## Run

```bash
python3 weather.py
```

No third-party Python packages are required. Tkinter is included with the
normal macOS Python distribution. If your Python build does not include
Tkinter, install a Python distribution that includes the Tk framework.

## Safe workflow

1. For a first run, optionally enable **Test mode · preview only** to preview changes.
2. Choose the Aerofly FS 4 folder if the detected path is not correct.
3. Enter an ICAO code and click **Load METAR**.
4. Review the decoded report and the exact fields shown in the change log.
5. Close Aerofly FS 4.
6. Uncheck test mode and click **Apply Weather to Aerofly**.

Every real apply creates and verifies a timestamped sibling backup before
writing. The new file is staged, flushed, structurally checked, and replaced
atomically. **Restore Backup** uses the newest `main.mcf.backup-*` file.

## Test

```bash
python3 -m unittest discover -s tests -v
python3 -m py_compile weather.py gui.py metar.py aerofly.py logging_utils.py
```

The tests use temporary copies and never touch a user's real Aerofly folder.

## Conversion notes

* Wind strength is normalized with 50 kt as a full-scale reference; 12 kt
  becomes 0.24. This is isolated in `metar_wind_to_aerofly_strength`.
* Visibility is normalized linearly from 0–10,000 metres; `9999` and `CAVOK`
  are treated as 10 km or greater and become 1.0.
* Cloud bases are normalized by layer family (15,000 ft lower, 30,000 ft
  middle, 40,000 ft high) and mapped to the existing Aerofly cloud fields.
* A report with no cloud group preserves the current Aerofly cloud state.
  Explicit `CLR`, `SKC`, `NSC`, or `NCD` reports clear cloud density.

The application writes `weather_injector.log` next to the launcher with
timestamps, reports, conversions, backups, and errors.
