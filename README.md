# File Integrity Monitor

A small command line tool that records the state of a folder and later
tells you exactly what changed: which files were added, modified or
deleted. It is a simplified version of what tools such as AIDE or Tripwire
do.

## Why I built it

An unexpected change in a configuration folder, a web root or a shared
drive is one of the oldest signs of tampering, and also of honest
mistakes. Integrity is one third of the CIA triad (confidentiality,
integrity, availability), and this project makes that idea concrete with
cryptographic hashes.

## How it works

1. `baseline` walks the folder and stores the SHA-256 hash, size and
   modification time of every file in `baseline.json`.
2. `check` scans the folder again and compares it with the baseline. A
   file counts as modified when its hash changed, even if the timestamp
   was forged.
3. The exit code is `0` when nothing changed and `1` when something did,
   so the check can be scheduled (cron or Task Scheduler) and used to
   trigger an alert.

## Usage

```bash
# Record the current state of a folder
python fim.py baseline ./my_folder

# Later, check what changed
python fim.py check ./my_folder

# Use a custom baseline location
python fim.py check ./my_folder --baseline /safe/place/baseline.json
```

No external dependencies, only the Python 3 standard library.

## Example output

```
Integrity check against baseline from 2026-10-27T22:04:57
[ADDED] backdoor.php
[MODIFIED] config.ini
[DELETED] sub/data.txt

3 change(s) detected.
```

## Limitations

- The baseline file itself must be protected. If an attacker can edit it,
  the check can be fooled, so in real use it should be stored read-only or
  on another system.
- It detects changes after the fact. It does not prevent them or tell you
  who made them.

## Next steps

- [ ] Ignore patterns (for example log files that change by design)
- [ ] Sign the baseline so tampering with it is detectable
- [ ] Send an email or webhook alert when changes are found
- [ ] Report which user and process changed a file where the OS allows it

## Tech stack

- Python 3 (standard library: `hashlib`, `pathlib`, `json`, `argparse`)
"# file-integrity-monitor" 
