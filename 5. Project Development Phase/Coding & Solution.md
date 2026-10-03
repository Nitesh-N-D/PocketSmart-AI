# Coding & Solution

This document walks through the key implementation pieces of `Project/`, with real excerpts from the committed code.

## 1. JSON extraction from Gemini responses

Gemini sometimes wraps JSON in markdown fences or adds commentary. `extract_json_from_response` strips fences and pulls the outermost `{...}` block, raising a clean `ValueError` instead of leaking a raw stack trace to the route layer:

```python
def extract_json_from_response(text: str) -> dict:
    cleaned = text.strip()
    cleaned = re.sub(r"^```(?:json)?", "", cleaned.strip(), flags=re.IGNORECASE).strip()
    cleaned = re.sub(r"```$", "", cleaned.strip()).strip()

    start = cleaned.find("{")
    end = cleaned.rfind("}")
    if start == -1 or end == -1 or end < start:
        raise ValueError("No JSON object found in Gemini response")

    json_str = cleaned[start:end + 1]
    try:
        return json.loads(json_str)
    except json.JSONDecodeError as exc:
        raise ValueError(f"Could not parse JSON from Gemini response: {exc}") from exc
```

## 2. Multi-model fallback chain with retry

Every planner routes its Gemini call through `_generate_with_retry`, which walks a cost-ordered list of models (`MODEL_CANDIDATES = ["gemini-3.5-flash-lite", "gemini-2.5-flash-lite", "gemini-flash-lite-latest"]`). A `NotFound` (model retired/unavailable for this key) skips immediately to the next candidate; any other error (e.g. quota exhaustion) retries once on the same model before moving on. Only if every candidate fails does the caller fall back to static data:

```python
def _generate_with_retry(parts) -> str:
    last_exc = None
    for model_name in MODEL_CANDIDATES:
        model = genai.GenerativeModel(model_name)
        for attempt in range(2):
            try:
                response = model.generate_content(parts)
                return response.text
            except NotFound as exc:
                last_exc = exc
                logger.warning("Model %s unavailable for this key, trying next candidate", model_name)
                break
            except Exception as exc:
                last_exc = exc
                if attempt == 0:
                    time.sleep(1)
    raise last_exc
```

## 3. Multimodal jewelry recommendations

When an outfit image is uploaded, the jewelry planner passes both the prompt text and a `PIL.Image` into `generate_content`, which is Gemini 3.5 Flash-Lite's multimodal call path:

```python
if image_path:
    img = Image.open(image_path)
    prompt = base_prompt + "\n\nAn image of the outfit is uploaded. Suggest jewelry that complements it..."
    raw_text = _generate_with_retry([prompt, img])
else:
    raw_text = _generate_with_retry(prompt)
```

## 4. Category-aware shopping links (party planner)

Each party budget category maps to a different set of relevant Indian platforms (e.g. venues to booking/travel sites, catering to food delivery apps) rather than a single generic list:

```python
CATEGORY_PLATFORMS = {
    "venue": ["google", "booking", "makemytrip", "oyorooms", "nobroker"],
    "catering": ["swiggy", "zomato"],
    "decoration": ["amazon", "flipkart", "meesho", "myntra"],
    "entertainment": ["bookmyshow", "amazon", "flipkart"],
    ...
}
```

## 5. Auth dependency pattern

Every protected API route declares its auth requirement explicitly via `Depends`, rather than relying on middleware that could silently miss a new route:

```python
@app.post("/home-budget")
async def home_budget(
    budget_input: HomeBudgetInput, username: str = Depends(get_current_active_user)
):
    ...
```

## 6. Fallback recommendations

If Gemini is unreachable, rate-limited/quota-exhausted, or returns invalid JSON, each planner returns a static, deterministically-computed INR budget split marked `"source": "fallback"`, so the UI always has something structured to render. The exception is logged server-side (via the `pocketsmart.gemini` logger) rather than silently swallowed, so a quota/auth/parsing failure is visible in the uvicorn console:

```python
try:
    raw_text = _generate_with_retry(prompt)
    result = extract_json_from_response(raw_text)
except Exception as exc:
    logger.warning("Home planner falling back to static data: %s", exc)
    return _home_fallback(budget_input)
```
