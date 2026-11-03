# Contributing

## Running Lighthouse

Lighthouse is installed globally (`npm install -g lighthouse`). Use the local Chromium binary and `xvfb-run` for headless execution.

### Prerequisites

```bash
python3 -m http.server 8080 --directory _site &
```

### Light Mode

```bash
xvfb-run --auto-servernum lighthouse http://localhost:8080/ \
  --chrome-path=/usr/bin/chromium \
  --chrome-flags="--headless --disable-gpu --no-sandbox --disable-site-isolation-trials --disable-features=site-per-process" \
  --quiet --output=json --output-path=/tmp/lh_light.json
```

### Dark Mode

```bash
xvfb-run --auto-servernum lighthouse http://localhost:8080/ \
  --chrome-path=/usr/bin/chromium \
  --chrome-flags="--headless --disable-gpu --no-sandbox --disable-site-isolation-trials --disable-features=site-per-process --force-color-scheme=dark" \
  --quiet --output=json --output-path=/tmp/lh_dark.json
```

### Reading Results

```bash
python3 -c "
import json
d = json.load(open('/tmp/lh_light.json'))
for cat, info in d['categories'].items():
    if info.get('score') is not None:
        print(f'{cat}: {info[\"score\"]}')
for audit in ['image-delivery-insight', 'cumulative-layout-shift', 'render-blocking-insight', 'unminified-javascript', 'unused-javascript', 'color-contrast']:
    if audit in d['audits']:
        print(f'{audit}: {d[\"audits\"][audit].get(\"score\")}')
"
```

### Build the Site

```bash
npx eleventy
```