# Working agreements

## Development style

- Keep solutions simple.
- Prefer KISS and DRY.
- Avoid unnecessary abstraction.
- Avoid unnecessary object-oriented designs.
- Prefer small, pure functions and functional composition where practical.
- Do not introduce classes unless they clearly reduce complexity.
- Do not add frameworks, libraries, services, config layers, or architecture unless explicitly requested.

## Python style

- Write pylint-compatible Python.
- Use clear function names, explicit parameters, and readable control flow.
- Add docstrings for every public module, class, and function.
- Add short comments only where they explain non-obvious intent, constraints, tradeoffs, or edge cases.
- Do not add comments that merely repeat the code.
- Prefer type hints for function signatures.
- Keep functions small and focused.
- Avoid global mutable state.
- Avoid hidden side effects where practical.

## Tests and validation

- Do not create test cases unless explicitly requested.
- Do not run test suites unless explicitly requested.
- Do not run broad validation commands unless explicitly requested.
- For simple edits, reason locally and make the minimal change.
- If validation is necessary for safety, ask before running it.

## Security and privacy

- Do not read, summarize, modify, print, grep, cat, copy, or infer from sensitive files.
- Treat env files, private keys, certificates, tokens, credentials, private folders, generated CSV exports, and private Excel files as out of scope.
- If a task appears to require a denied file, stop and ask for a sanitized excerpt instead.