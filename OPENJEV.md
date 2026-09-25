# OpenJEV Support

This fork adds optional [OpenJEV](https://openjev.sh) support alongside the original [TypeSafe](https://typesafe.ai) Jev integration. TypeSafe remains the default; OpenJEV is opt-in.

## What was added

- **`src/config.ts`** — Added `OPENJEV_ENDPOINT` and `OPENJEV_MODEL` constants; extended `parseEnvironment()` with provider selection logic (`JEV_PROVIDER` env var + key-based auto-detection); added `OPENJEV_API_KEY` to the auth fallback chain and error message.
- **`README.md`** — Added OpenJEV note after the project intro and a new "OpenJEV provider" subsection under Configuration documenting the selection rule and env vars.
- **`OPENJEV.md`** — This file.

No TypeSafe code was removed, renamed, or re-defaulted. The `retryableStatus()` function in `src/jev.ts` already covers HTTP 503 (via `status >= 500`) and 429, so no change was needed there.

## Provider selection rule

1. **Explicit choice wins** — `JEV_PROVIDER=openjev` forces OpenJEV; `JEV_PROVIDER=typesafe` forces TypeSafe.
2. **TypeSafe if its key is set** — when `JEV_API_KEY` or `TYPESAFE_API_KEY` is present and no explicit provider is set, TypeSafe is used (unchanged default).
3. **OpenJEV if only `OPENJEV_API_KEY` is set** — auto-selected when no TypeSafe key is found.

When OpenJEV is selected:
- Endpoint: `https://api.openjev.sh/v1/systemone`
- Model: `openjev`
- Key env: `OPENJEV_API_KEY`

Explicit `JEV_ENDPOINT` / `JEV_MODEL` overrides always take precedence.

## How to configure

```bash
# Option A: explicit provider
export JEV_PROVIDER=openjev
export OPENJEV_API_KEY=your_openjev_key

# Option B: auto-detected (only OPENJEV_API_KEY set, no TypeSafe key)
export OPENJEV_API_KEY=your_openjev_key
```

## Verification

A live POST request was made to `https://api.openjev.sh/v1/systemone` with model `openjev`, state `ping`, and one noul question. The endpoint returned HTTP 200 with a valid answers object, confirming the OpenJEV gateway works with this project's request contract.

A grep confirmed no hardcoded `api.typesafe.ai` default was introduced — the original TypeSafe default remains as the fallback when no provider is selected.

## Upstream

Original project: https://github.com/XYenon/ajevt-browser by @XYenon
