---
tags: [tutorial, python, pytest]
aliases: [Python TDD tutorial, CLI tutorial]
created: 2026-10-26
level: beginner
time: 2h
---
# Tutorial - Python CLI Tool with TDD

> [!abstract] What you'll build
> `logstats` — a command-line tool that reads a web server access log and prints the top paths, status-code counts and error rate. Built test-first with pytest: **red → green → refactor** for every feature.

**Prerequisites:** Python basics. **Notes used:** [[Test-Driven Development]], [[pytest MOC]], [[Python Generators and Iterators]].

Sample log line (Common Log Format):
```
127.0.0.1 - - [26/Oct/2026:10:00:01 +0000] "GET /api/items HTTP/1.1" 200 512
```

## Step 1 — Setup
```bash
uv init --package logstats && cd logstats
uv add --dev pytest
mkdir tests
```

## Step 2 — 🔴 First test: parse one line
```python
# tests/test_parse.py
from logstats.parse import parse_line, LogEntry

def test_parses_common_log_format_line():
    line = '127.0.0.1 - - [26/Oct/2026:10:00:01 +0000] "GET /api/items HTTP/1.1" 200 512'
    assert parse_line(line) == LogEntry(ip="127.0.0.1", method="GET", path="/api/items", status=200, size=512)
```
`uv run pytest` → **fails** (module doesn't exist). Good — that's red.

## Step 3 — 🟢 Make it pass
```python
# src/logstats/parse.py
import re
from dataclasses import dataclass

LINE_RE = re.compile(r'(?P<ip>\S+) \S+ \S+ \[[^\]]+\] "(?P<method>\S+) (?P<path>\S+) [^"]*" (?P<status>\d{3}) (?P<size>\d+|-)')

@dataclass(frozen=True)
class LogEntry:
    ip: str
    method: str
    path: str
    status: int
    size: int

def parse_line(line: str) -> LogEntry:
    m = LINE_RE.match(line)
    return LogEntry(m["ip"], m["method"], m["path"], int(m["status"]), int(m["size"]))
```
Green. ✅

## Step 4 — 🔴🟢 Edge cases with parametrize
```python
import pytest

@pytest.mark.parametrize("line", ["", "garbage", '1.2.3.4 - - [x] "GET" 200 1'])
def test_invalid_lines_return_none(line):
    assert parse_line(line) is None

def test_dash_size_means_zero():
    line = '1.1.1.1 - - [26/Oct/2026:10:00:01 +0000] "HEAD / HTTP/1.1" 304 -'
    assert parse_line(line).size == 0
```
Fix the implementation:
```python
def parse_line(line: str) -> LogEntry | None:
    m = LINE_RE.match(line)
    if not m:
        return None
    size = 0 if m["size"] == "-" else int(m["size"])
    return LogEntry(m["ip"], m["method"], m["path"], int(m["status"]), size)
```

## Step 5 — 🔴🟢 Statistics
```python
# tests/test_stats.py
import pytest
from logstats.parse import LogEntry
from logstats.stats import summarize

def e(path="/", status=200):
    return LogEntry("1.1.1.1", "GET", path, status, 10)

def test_summary_counts():
    s = summarize([e("/a"), e("/a"), e("/b", 500), e("/c", 404)])
    assert s.total == 4
    assert s.top_paths(2) == [("/a", 2), ("/b", 1)]
    assert s.status_classes == {"2xx": 2, "4xx": 1, "5xx": 1}
    assert s.error_rate == pytest.approx(0.25)     # only 5xx count as errors

def test_empty_input():
    s = summarize([])
    assert s.total == 0 and s.error_rate == 0.0
```
```python
# src/logstats/stats.py
from collections import Counter
from collections.abc import Iterable
from dataclasses import dataclass, field
from logstats.parse import LogEntry

@dataclass
class Summary:
    total: int = 0
    paths: Counter = field(default_factory=Counter)
    status_classes: dict[str, int] = field(default_factory=dict)
    errors: int = 0

    def top_paths(self, n: int) -> list[tuple[str, int]]:
        return self.paths.most_common(n)

    @property
    def error_rate(self) -> float:
        return self.errors / self.total if self.total else 0.0

def summarize(entries: Iterable[LogEntry]) -> Summary:
    s = Summary()
    classes = Counter()
    for entry in entries:                      # works with generators → constant memory
        s.total += 1
        s.paths[entry.path] += 1
        classes[f"{entry.status // 100}xx"] += 1
        s.errors += entry.status >= 500
    s.status_classes = dict(classes)
    return s
```

## Step 6 — 🔴🟢 The CLI
```python
# tests/test_cli.py
from logstats.cli import main

LOG = """\
1.1.1.1 - - [26/Oct/2026:10:00:01 +0000] "GET /a HTTP/1.1" 200 5
1.1.1.1 - - [26/Oct/2026:10:00:02 +0000] "GET /a HTTP/1.1" 500 5
not a log line
"""

def test_cli_prints_report(tmp_path, capsys):
    log = tmp_path / "access.log"
    log.write_text(LOG)
    exit_code = main([str(log), "--top", "1"])
    out = capsys.readouterr().out
    assert exit_code == 0
    assert "Requests: 2" in out
    assert "/a  2" in out
    assert "Error rate: 50.0%" in out

def test_cli_missing_file(capsys):
    assert main(["nope.log"]) == 2
    assert "not found" in capsys.readouterr().err
```
```python
# src/logstats/cli.py
import argparse, sys
from pathlib import Path
from logstats.parse import parse_line
from logstats.stats import summarize

def read_entries(path: Path):
    with path.open(encoding="utf-8", errors="replace") as f:
        for line in f:
            if (entry := parse_line(line.rstrip("\n"))) is not None:
                yield entry

def main(argv: list[str] | None = None) -> int:
    parser = argparse.ArgumentParser(prog="logstats", description="Summarize an access log")
    parser.add_argument("logfile", type=Path)
    parser.add_argument("--top", type=int, default=5)
    args = parser.parse_args(argv)

    if not args.logfile.exists():
        print(f"error: {args.logfile} not found", file=sys.stderr)
        return 2

    s = summarize(read_entries(args.logfile))
    print(f"Requests: {s.total}")
    print(f"Error rate: {s.error_rate:.1%}")
    print("Status classes:", ", ".join(f"{k}={v}" for k, v in sorted(s.status_classes.items())))
    print("Top paths:")
    for path, count in s.top_paths(args.top):
        print(f"  {path}  {count}")
    return 0

if __name__ == "__main__":
    raise SystemExit(main())
```
Register the command in `pyproject.toml`:
```toml
[project.scripts]
logstats = "logstats.cli:main"
```
```bash
uv run pytest -v
uv run logstats access.log --top 3
```

> [!check] Checkpoint — all tests green, and you never wrote a line of production code without a failing test first.

## Step 7 — 🔵 Refactor
With tests as a safety net: add type hints everywhere, run `uvx ruff check --fix` and `uvx mypy src`, extract the report formatting into `format_report(summary) -> str` and test it directly.

## 🏋️ Challenges
1. Add `--since 2026-10-26T10:00` filtering (parse the timestamp; freeze test data, not the clock).
2. Read from stdin when the file is `-` (`cat access.log | logstats -`).
3. Add `--json` output and test it with `json.loads`.
4. Property-based test with Hypothesis: `summarize` total always equals input length.
5. Run tests on Windows and Linux in GitHub Actions.

> [!success]- Solution sketch: challenge 2
> ```python
> parser.add_argument("logfile")
> ...
> source = sys.stdin if args.logfile == "-" else open(args.logfile, encoding="utf-8")
> entries = (e for line in source if (e := parse_line(line.rstrip("\n"))))
> # test: monkeypatch.setattr("sys.stdin", io.StringIO(LOG)); main(["-"])
> ```

## Related
- [[Tutorials MOC]] · [[pytest Parametrize]] · [[pytest Built-in Fixtures]]
