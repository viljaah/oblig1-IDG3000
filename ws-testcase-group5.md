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
- CO₂ / Energy: Significantly reduces data transfer by loading media only when users explicitly request it. Many users never interact with all media on a page, so this saves substantial data and energy.
- UX / Accessibility: A quick-loading page creates a more positive first impression and enhances overall user satisfaction. Users are less likely to leave a fast-loading site, which leads to higher engagement and imrpoved user experience.


## 4) Machine-testable? (partly)
**Can be automated:**
- Detection of images without `loading="lazy"` attribute using Lighthouse. [Source](https://web.dev/articles/browser-level-image-lazy-loading). [Source](https://developer.mozilla.org/en-US/docs/Web/Performance/Guides/Lazy_loading#images_and_iframes).
- Performance metrics measurement (FCP, LCP, page weight) before and after implementing deferred loading.

**Requires manual:**
- Check if images actually defer loading until scrolled into view.
- User experience impact: whether deferred loading causes layout shifts/facade with help from elements or delays.

## 5) Signals to check (explicit list)
- Media elements (video, audio, images) load behind facade elements on initial page load
- Media files only requested after user interaction (click/tap)
- Facade elements provide visual indication they're interactive (cursor: pointer, play buttons)
- Network requests show clear difference: initial load vs post-interaction load
- transferSize (kB, mB, etc) is smaller for pages that use facades (like thumbnails or previews), since only the facade data is counted. For media without a facade, the full file size is included in transferSize

## 6) Pass / Fail rules (explicit)
- **PASS if**: Heavy media loads behind facade elements AND media files are not requested until user interaction
- **FAIL if**: Heavy media loads immediately on page load without user interaction

## 7) Exact test steps (reproducible)
**Structured:**
1. Serve from project root: `cd oblig1-IDG3000 && npx http-server . -p 8000`
2. Run Lighthouse audit on broken version: 
```bash
npx lighthouse 'http://localhost:8000/demo/broken/broken.html' \
--output=json \
--output-path=./evidence/audit-broken-with-videos.json \
--throttling-method=simulate \
--emulated-form-factor=mobile \
--save-assets \
--chrome-flags="--headless"
```

3. Serve fixed version: `cd oblig1-IDG3000 && npx http-server . -p 8001` 
4. Run Lighthouse on fixed (before interaction): 
`npx lighthouse 'http://localhost:8000/demo/fixed/fixed.html' \
--output=json \
--output-path=./evidence/audit-fixed-with-videos.json \
--throttling-method=simulate \
--emulated-form-factor=mobile \
--save-assets \
--chrome-flags="--headless"``
5. run metrics commands listed in summary.md (to see results of LCP, total request, transferSize, and lighthouse viewport settings)

**Visual:**
1. Open each HTML file on vscode go live extencion or by this command `cd demo/broken && npx http-server . -p 8000` and `cd demo/fixed && npx http-server . -p 8001` 
2. Open DevTools, Network tab
3. Refresh the page


## 8) Evidence required (list filenames)
- evidence/audit-broken-with-vidoes.json (main audit results)
- evidence/audit-fixed-with-vidoes.json (main audit results)
- evidence/audit-broken.devtoolslog.json (detailed network log)
- evidence/audit-fixed.devtoolslog.json (detailed network log)
- evidence/audit-broken.trace.json (performance trace)
- evidence/audit-fixed.trace.json (performance trace)
- evidence/network-broken-before.png (screenshot)
- evidence/network-broken-after.png (screenshot)
- evidence/network-fixed-before.png (screenshot)
- evidence/network-fixed-after.png (screenshot)
- evidence/summary.md (analysis)


## 10) Assumptions & notes
- **Updated evidence**: Tests run from project root to ensure media files (videos/audio) are accessible and measured
- **Viewport**: Lighthouse uses 412px x 823px mobile viewport (confirmed via configSettings.screenEmulation)
- **Dramatic impact**: With media files properly loading, deferred loading saves 3.58MB (96% reduction)
- **Media file behavior**: Broken version loads 1.77MB video + 1.63MB audio immediately; Fixed version defers all heavy media with preload="none"
- **Transfer size variance**: CLI measurements are primary evidence due to controlled conditions; DevTools screenshots demonstrate loading behavior patterns
- **Evidence hierarchy**: Lighthouse audit data provides quantitative metrics; DevTools screenshots show qualitative user experience differences
- **Tools**: Lighthouse CLI [12.8.2], Node.js [v22.13.0], Chrome
- **Initial load definition**: All resources downloaded automatically when page opens, before any user interactions
- **Deferred loading definition**: Media content loads only when user explicitly clicks/interacts with facade elements
- **Network conditions**: Default Lighthouse audit settings (not specifically tested on slow networks). In command line we used this [--throttling-method=simulate] which Lighthouse automatically applies simulated slow network conditions. 
