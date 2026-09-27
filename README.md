# ProdDoctor-Test

Public, reproducible demos for [ProdDoctor](https://github.com/lucaswenbo/ProdDoctor).

## Run the v2.1.1 failure-diagnosis demo

You do not need a website, Cloudflare account, or any secrets. The workflow starts a deterministic local fixture server inside GitHub Actions and runs the released `lucaswenbo/ProdDoctor@v2.1.1` against it.

**Run it yourself:**

1. Open **Actions**.
2. Choose **ProdDoctor v2.1.1 Failure Diagnosis Demo**.
3. Click **Run workflow** → **Run workflow**.
4. Open the completed run and expand the numbered ProdDoctor steps.

Direct workflow page:

https://github.com/lucaswenbo/ProdDoctor-Test/actions/workflows/proddoctor-v2-1-regression.yml

### What it reproduces

| Scenario | Expected ProdDoctor result |
|---|---|
| Healthy production path | PASS, no invented cause |
| Cloudflare Challenge / WAF | Identifies edge/WAF blocking |
| HTTP 201 when 200 is expected | Reports the actual vs expected status |
| Same-origin JavaScript returns 404 | Points to the broken JS/CSS asset layer |
| Real Chromium page throws an uncaught JS error | Points to the browser runtime |
| Target gives no response while `expect` is configured | Reports a request failure, not a content mismatch |

The last case is the regression fixed in **v2.1.1**. In v2.1.0, a target that never responded could be described as if the page had responded with the wrong content. v2.1.1 now gives the request failure priority.

Expected output for that case:

```text
Result: ❌ FAIL
Likely cause: The production request failed before a valid response was received.

Page: no response
Blocking issues:
- Request failed: fetch failed
```

The intentionally failing ProdDoctor steps use `continue-on-error`, so the demo workflow itself can finish successfully after proving each failure path.

## Full integration suite

The separate **ProdDoctor Integration Suite** runs v2.1.1 against a real public target and also verifies Chromium evidence files such as `browser.png`, `report.html`, `report.json`, and `trace.zip`.

https://github.com/lucaswenbo/ProdDoctor-Test/actions/workflows/proddoctor-suite.yml

Main project: https://github.com/lucaswenbo/ProdDoctor
