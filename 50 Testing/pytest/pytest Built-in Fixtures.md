---
tags: [pytest, testing, snippet]
aliases: [tmp_path, capsys, caplog, monkeypatch fixture]
created: 2026-10-26
status: evergreen
---
# pytest Built-in Fixtures

| Fixture | Provides |
|---|---|
| `tmp_path` | unique `pathlib.Path` temp dir per test (`tmp_path_factory` for session) |
| `monkeypatch` | set/del attributes, env vars, dict items, cwd — auto-undone → [[pytest Mocking]] |
| `capsys` / `capfd` | captured stdout/stderr |
| `caplog` | captured `logging` records |
| `request` | info about the requesting test, `request.param`, `request.node` |
| `pytestconfig` | access CLI options / ini values |
| `recwarn` | recorded warnings |

```python
def test_writes_report(tmp_path):
    out = tmp_path / "report.csv"
    write_report(out)
    assert out.read_text().startswith("id,name")

def test_cli_prints_help(capsys):
    main(["--help"])
    assert "Usage" in capsys.readouterr().out

def test_logs_warning(caplog):
    with caplog.at_level("WARNING"):
        process(bad_row)
    assert "skipping row" in caplog.text

def test_reads_env(monkeypatch):
    monkeypatch.setenv("API_URL", "http://test")
    assert Settings().api_url == "http://test"
```

## Related
- [[pytest Fixtures]]
