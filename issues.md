# Lab 5 – Issues Found (student_grade_system.py)

Line numbers refer to the **original** file. Sorted high severity first.

| Issue | Type | Line(s) | Description | Fix Approach |
|-------|------|---------|-------------|--------------|
| `os.system` with string concatenation (Bandit B605, High) | Security | 60 | Filename is joined into a shell command, so a crafted filename could inject commands. | Removed the shell call and used `print()` for the message. |
| Weak MD5 hash (Bandit B324, High) | Security | 49 | MD5 is broken and shouldn't be used to hash identifiers. | Switched to `hashlib.sha256`. |
| `eval()` on caller-supplied string (Bandit B307, Medium / Pylint W0123) | Security | 44 | `compute_weighted_gpa` ran arbitrary code from `gpa_expr`. | Replaced the expression string with a numeric `max_gpa` parameter and computed `avg / 100 * max_gpa`. |
| Hardcoded token (Bandit B105, Low) | Security | 64 | Admin portal token was in source code. | Read it from the `GRADE_PORTAL_TOKEN` environment variable. |
| Token printed to console (found by reading the output) | Security | 72 | The program printed the secret token. | Now prints only whether a token is configured. |
| Weak PRNG for PIN (Bandit B311, Low) | Security | 53 | `random.randint` is predictable, bad for registration PINs. | Used `secrets.randbelow(9000) + 1000`. |
| Mutable default argument `grade_list=[]` (Pylint W0102) | Bug | 15 | The same list is shared across all calls, so logs pile up between unrelated calls. | Default is `None`, and a new list is created inside the function. |
| Bare `except: pass` (Bandit B110, Pylint W0702, Flake8 E722) | Bug | 26-27 | Hides every error, so a bad student id silently does nothing. | Catch `KeyError` only and return `False`, `True` on success. |
| GPA out of range (output looked wrong, no tool flags it) | Logic | 44, 68 | `avg_score / 20.0` gave a GPA of 4.35, above the 4.0 maximum. | Scale the 0-100 average onto a 4.0 scale, so S01 now gets 3.48. |
| File not closed safely, no encoding (Pylint R1732, W1514) | Bug | 57-59 | `open()`/`close()` leaks the file if `write` fails; encoding is unspecified. | Used `with open(..., encoding="utf-8")`. |
| Grade sheet written as `str(dict)` | Logic | 58 | Output is a Python repr, not a readable data format (and `json` was imported but never used). | Used `json.dump(..., indent=2)`. |
| Marks only checked for `< 0` | Logic | 16 | A mark such as 500 was accepted. | Only marks from 0 to 100 are accepted. |
| Unused variables `grace_marks`, `avg_score` (Pylint W0612, Flake8 F841) | Quality | 31, 43 | Dead code. | Removed `grace_marks`; `avg_score` is now used in the GPA. |
| Unused import `json` (Pylint W0611, Flake8 F401) | Quality | 6 | Imported but never used. | Now used by the grade sheet export. |
| `== None` (Pylint C0121, Flake8 E711) | Style | 32 | Singletons should be compared with `is`. | Changed to `is None`. |
| Redundant `if/else` returning bool (Pylint R1703, R1705) | Style | 35-38 | Can be a single return. | `return avg >= pass_threshold`. |
| `addGrade` not snake_case (Pylint C0103) | Style | 15 | Violates PEP 8 naming. | Renamed to `add_grade` and updated the call. |
| `%` string formatting (Pylint C0209) | Style | 19 | Old formatting style. | Used an f-string. |
| Missing docstrings (Pylint C0116) | Style | 15-63 | Functions undocumented. | Added one-line docstrings. |
| Long lines (Flake8 E501) | Style | 19, 34 | Over 79 characters. | Wrapped / split with a `marks` variable. |
| No newline at end of file (Pylint C0304, Flake8 W292) | Style | 75 | File ends without a newline. | Added the final newline. |

## Noticed but left alone
- `main()` still runs on import (no `if __name__ == "__main__":` guard); I kept this to match the original behaviour.
- `record_attendance` adds present days to what looks like a percentage, so it can exceed 100; I didn't change that.
- `grace_marks = 5.0` was never applied to the passing check, so I removed it instead of guessing at the intended policy.

## Results

| Tool | Before | After |
|------|--------|-------|
| Pylint | 5.51/10 | 10.00/10 |
| Bandit | 6 issues (2 High, 1 Medium, 3 Low) | 0 |
| Flake8 | 8 | 0 |
