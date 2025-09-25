# STAR Test-case: [WSG 2.16] — Deferred Loading of Media

Group: Vilja Henriksen, Modesta trakselyte
Date: 2025-09-25

## 1) WSG criterion (exact quote)
> "Load data-intensive media on the client side, including the media itself, behind a facade - a non-functional and static representational element."

## 2) Plain-language summary
Only load images and videos when the user is about to see them (lazy loading), instead of loading all media files immediately when the page loads.

## 3) Why it matters
- Performance: Reduces initial page load time and improves Core Web Vitals
- CO₂ / Energy: Less data transferred initially means lower emissions per page view
- UX / Accessibility: Faster initial loading, especially on slow connections or older devices

## 4) Machine-testable? (partly)
Automatable: Detect presence of `loading="lazy"` attributes, measure initial bytes transferred
Manual required: Verify images actually defer loading until scrolled into view

## 5) Signals to check (explicit list)
- `loading="lazy"` attribute present on below-the-fold images
- Initial `transferSize` for images that are not immediately visible
- Number of network requests on initial page load vs after scrolling
- Largest Contentful Paint (LCP) timing

## 6) Pass / Fail rules (explicit)
- PASS if: Below-the-fold images have `loading="lazy"` AND initial transferSize is <50% of total page images
- FAIL if: All images load immediately regardless of viewport position

## 7) Exact test steps (reproducible)
1. Serve broken version: `cd demo/broken && npx http-server . -p 8000`
2. Run Lighthouse: `npx lighthouse 'http://localhost:8000' --output=json --output-path=evidence/audit-broken.json`
3. Extract network data: `node tools/extract-requests.js evidence/audit-broken.json > evidence/requests-broken.csv`
4. Repeat for fixed version on port 8001
5. Compare initial bytes transferred and number of requests

## 8) Evidence required (list filenames)
- `evidence/audit-broken.json`, `evidence/audit-fixed.json`
- `evidence/requests-broken.csv`, `evidence/requests-fixed.csv` 
- `evidence/before.png`, `evidence/after.png`
- `evidence/summary.md`

## 9) Automation hints (optional)
Use script to count images with/without `loading="lazy"` and calculate bytes saved

## 10) Assumptions & notes
CO₂ model: Using transferSize as basis for emissions calculation
Testing on mobile viewport (375px width)