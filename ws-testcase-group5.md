# STAR Test-case: [WSG 2.16] — Deferred Loading of Media

Group: Vilja Henriksen, Modesta Trakselyte
Date: 2025-09-25

## 1) WSG criterion (exact quote)
- [2.16 Optimize media for sustainability](https://w3c.github.io/sustainableweb-wsg/#optimize-media-for-sustainability)
- Success Criterion: Deferred loading
> "Load data-intensive media on the client side, including the media itself, behind a facade - a non-functional and static representational element."

## 2) Plain-language summary
Don’t force the browser to download a giant video right away. Instead, load a small static image or “fake” play button, or "blury" element, and only load the actual video or other media content if the user clicks it, or interacts with it. "exmaples, youtube, instagram, booking.com"

## 3) Why it matters
- Performance: Deferred loading prevents heavy media from blocking initial page render and reduces Time to Interactive (TTI). Only essential content loads initially, improving Core Web Vitals.
- CO₂ / Energy: Significantly reduces data transfer by loading media only when users explicitly request it. Many users never interact with all media on a page, so this saves substantial bandwidth and energy.
- UX / Accessibility: A quick-loading page creates a more positive first impression and enhances overall user satisfaction. Users are less likely to leave a slow-loading site, which leads to higher engagement and retention and imrpoved user experience. 


## 4) Machine-testable? (partly)
**Can be automated:**
- Detection of images without `loading="lazy"` attribute using Lighthouse. [Source](https://web.dev/articles/browser-level-image-lazy-loading). [Source](https://developer.mozilla.org/en-US/docs/Web/Performance/Guides/Lazy_loading#images_and_iframes).
- Performance metrics measurement (FCP, LCP, page weight) before and after implementing deferred loading.

**Requires manual:**
- Check if images actually defer loading until scrolled into view.
- User experience impact: whether deferred loading causes layout shifts/facade with help from elements or delays.

## 5) Signals to check (explicit list)
- Media elements (video, audio, large images) load behind facade elements on initial page load
- Initial `transferSize` includes only facade assets, not full media files
- Media files only requested after user interaction (click/tap)
- Facade elements provide visual indication they're interactive (cursor: pointer, play buttons)
- Network requests show clear difference: initial load vs post-interaction load

## 6) Pass / Fail rules (explicit)
- **PASS if**: Heavy media (>500KB) loads behind facade elements AND media files are not requested until user interaction AND facades provide clear interaction cues
- **FAIL if**: Heavy media loads immediately on page load without user interaction

## 7) Exact test steps (reproducible)
1. Serve broken version: `cd demo/broken && npx http-server . -p 8000`
2. Run initial Lighthouse audit: `npx lighthouse 'http://localhost:8000/broken.html' \
--output=json \
--output-path=./evidence/audit-fixed.json \
--throttling-method=simulate \
--emulated-form-factor=mobile \
--save-assets \
--chrome-flags="--headless"`

3. Serve fixed version: `cd demo/fixed && npx http-server . -p 8001` 
4. Run Lighthouse on fixed (before interaction): `npx lighthouse 'http://localhost:8001/fixed.html' --output=json --output-path=evidence/audit-fixed-initial.json`
5. run metrixs commands listed in summary.md (to see results of LCP, total request)


## 8) Evidence required (list filenames)
- evidence/audit-broken.json (main audit results)
- evidence/audit-fixed.json (main audit results)
- evidence/audit-broken.devtoolslog.json (detailed network log)
- evidence/audit-fixed.devtoolslog.json (detailed network log)
- evidence/audit-broken.trace.json (performance trace)
- evidence/audit-fixed.trace.json (performance trace)
- evidence/network-broken.png (screenshot)
- evidence/network-fixed.png (screenshot)
- evidence/summary.md (analysis)

## 9) Automation hints (optional)
- Detection of interactive elements (play buttons, clickable areas) that trigger media loading

## 10) Assumptions & notes
CO₂ model: Using transferSize as basis for emissions calculation
Testing on mobile viewport (375px width)


