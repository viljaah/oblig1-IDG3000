# STAR Test-case: [WSG 2.16] — Deferred Loading of Media

Group: Vilja Henriksen, Modesta Trakselyte
Date: 2025-09-25

## 1) WSG criterion (exact quote)
- [2.16 Optimize media for sustainability](https://w3c.github.io/sustainableweb-wsg/#optimize-media-for-sustainability)
- Success Criterion: Deferred loading
> "Load data-intensive media on the client side, including the media itself, behind a facade - a non-functional and static representational element."

## 2) Plain-language summary
Only load images and videos when the user is about to see them (lazy loading), instead of loading all media files immediately when the page loads.

## 3) Why it matters
- Performance: Lazy loading creates faster page loading by only downloading images when users actually scroll to see them, rather than loading everything at once. which improves LCP (largest contenful paint - one of the core web vitals). [source](https://www.cloudflare.com/ru-ru/learning/performance/what-is-lazy-loading/)
- CO₂ / Energy:  Less data transfer means reduced energy consumption across the entire network path - from user devices and WiFi routers to servers and data centers, meaning decreasing the carbon footprint per page load. [source](https://medium.com/@FoxTechnology/how-lazy-loading-can-improve-application-startup-time-7e5cbca14921)
- UX / Accessibility: Helps Application Load Faster + happy users → Faster loading improves user experience, especially for users on slow connections or limited data plans. [source](https://medium.com/@FoxTechnology/how-lazy-loading-can-improve-application-startup-time-7e5cbca14921)


## 4) Machine-testable? (partly)
**Can be automated:**
- Detection of images without `loading="lazy"` attribute using Lighthouse. [Source](https://web.dev/articles/browser-level-image-lazy-loading). [Source](https://developer.mozilla.org/en-US/docs/Web/Performance/Guides/Lazy_loading#images_and_iframes).
- Performance metrics measurement (FCP, LCP, page weight) before and after implementing lazy loading.

**Requires manual:**
- Check if images actually defer loading until scrolled into view.
- User experience impact: whether lazy loading causes layout shifts or delays.

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