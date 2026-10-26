---
tags: [pytest, testing, concept]
aliases: [monkeypatch, unittest.mock, pytest-mock, mocker, patch]
created: 2026-10-26
status: evergreen
---
# pytest Mocking

> [!summary] TL;DR
> **Patch where the name is looked up, not where it's defined.** If `app/services.py` does `from app.clients import send_email`, patch `app.services.send_email`.

## monkeypatch (built-in)
```python
def test_uses_fake_clock(monkeypatch):
    monkeypatch.setattr("app.billing.now", lambda: datetime(2026, 1, 1))
    monkeypatch.setenv("FEATURE_X", "1")
    monkeypatch.delenv("HOME", raising=False)
    monkeypatch.setitem(app.config, "DEBUG", True)
```

## unittest.mock via pytest-mock's `mocker`
```python
def test_signup_sends_email(mocker):
    send = mocker.patch("app.services.send_email", autospec=True)
    signup("ada@example.com")
    send.assert_called_once_with("ada@example.com", subject=mocker.ANY)

def test_retries_on_timeout(mocker):
    get = mocker.patch("app.client.httpx.get",
                       side_effect=[httpx.TimeoutException("t"), mocker.Mock(status_code=200)])
    assert fetch_status() == 200
    assert get.call_count == 2

def test_spy(mocker):
    spy = mocker.spy(Calculator, "add")
    Calculator().add(1, 2)
    spy.assert_called_once()
```

## Mock cheat sheet
| | |
|---|---|
| `m.return_value = x` | what call returns |
| `m.side_effect = Exc / [a, b] / fn` | raise / sequence / compute |
| `m.assert_called_once_with(...)` | verify |
| `m.call_args_list` | all calls |
| `autospec=True` | mock respects real signature (catches typos) |
| `AsyncMock` | for `async def` |

## HTTP
```python
import respx, httpx
@respx.mock
def test_github():
    respx.get("https://api.github.com/users/ada").mock(return_value=httpx.Response(200, json={"id": 1}))
    assert get_user("ada")["id"] == 1
```
(`responses` library for `requests`.)

## Related
- [[Test Doubles]] · [[Jest Mock Functions]] · [[Jest Module Mocking]]
