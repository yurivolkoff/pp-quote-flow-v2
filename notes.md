# Prototype Notes — 2026-09-09-quote-flow-v2
_Date: 2026-09-09 | Page: PDP + quote modal + issued quote (web/PDF) + email_

## Task

Build the Instant Quote v2 UI from the approved Phase 1 analysis: a production-faithful PDP with two new affordances, a 760px request modal, an issued quote that reads as a descendant of the customer-facing quote PDF, a print/PDF view over the same DOM, and the transactional email.

## Source

- Production PDP: https://www.promotionpros.com/10-oz-stainless-wine-tumbler-2 — saved DOM `design/htmls_saved/2026-09-09-wine-tumbler-pdp.html`, screenshots `design/library/screenshots/2026-09-09-tumbler-pdp-*`, captured 2026-09-09.
- Legacy quote PDF: `_sources/pp-quotes/2026-09-03-orderQuote-4-rep-issued.pdf`, rep-issued 06-23-2026. **Not rendered this session** — `pdftoppm` is not installed, so the read is the structure inventory in analysis §2, not a direct look at the file.
- Spec: `research/reports/2026-09-09-quote-v2-ux-analysis.md` (§7 = the build checklist).
- Copy: `design/prototypes/2026-09-03-quote-flow/spec.md` deck IDs + its notes "Copy addendum". Placeholder-grade; Phase 3 owns the editorial pass.

## Design Library

`design/library/promotionpros-pdp.md` (2026-09-09 full-page anatomy + 2026-09-03 configurator capture).

---

## Decisions from Phase 1 applied

| Analysis | How it is built |
|---|---|
| §3a1 sample row between carousel and Description | Full-width outline ghost, 52px, 1px `#EEEEEE`, 4px radius, navy label, no orange, no fill. Switchable eligible / not-eligible via chip, annotated as a demonstration. |
| §3a2 quote CTA under the purchase CTA | Same width as the orange CTA, 48px, transparent, 1px `#001542` border, navy 16px/700, 4px radius, with a 13px `#6C6C6C` helper. Orange CTA and the `Ask us` link untouched. |
| §3b modal at effort 1 | 760px, two columns, zero controls, price above the two fields, `Change` in the card header with its helper, amber default-decoration notice, breakdown disclosure carrying production's lines verbatim. |
| §3c price inversion | The flag holds the **total**; `Average price per unit` is the caption. Renames to `Total with delivery` when a rate is chosen. |
| §2.4 keep-list | Navy letterhead (retinted `#0F2145` → `#001542`), `#SKU – Name` heading with hero, checkmark-bulleted configuration, compact price table, single orange action, multi-service delivery table, closing thank-you line, address block. |
| §2.5 drop-list | No "prices are not final" disclaimer, no `$999,999.00` sentinel, no `Request Dates`, no admin pay link, no free-text Notes bullet, no Open Sans, no all-caps CTA, no per-page-per-product split. |
| §6.2 tier prices | No ladder. One band sentence in the expanded row: `Quantity break: 25–49: $11.96 each · 50+: $11.59 each`. |
| §6.1 specialist block | Named rep by default, chip switches to the sales line. Present on web, PDF and email. |
| §3c modules | Delivery is a checkbox that opens address → options → chosen and updates the totals and the flag. Details is a light expandable link. Both stamp a dated revision; neither issues a new number. |
| §3d PDF | A print stylesheet over the same DOM, plus a Letter-proportioned on-screen preview. |

## States and toggles

**View 1 — PDP.** Sample row eligible / not eligible · configurator closed / open (steps 3–4) · decoration defaulted / set by the buyer · priceable yes / no.
**View 2 — Modal.** Opens from the quote CTA or the view bar. Priced state and specialist state (driven by the PDP's priceable chip). Breakdown collapsed / expanded. Field validation on blur.
**View 3 — Issued quote.** Quote state valid · expiring · expired · ordered · invalid link. Delivery off · address · options · chosen · no rates. Details added. Specialist named / sales line. First-arrival band.
**View 4 — PDF.** Mirrors view 3's state. "Print this now" runs the real print stylesheet against view 3 from any view.
**View 5 — Email.** Mirrors view 3's state; an added module flips the subject to the `Updated:` prefix.

## Measurements (DOM-measured, not estimated)

| Measure | Value |
|---|---|
| Quote CTA top, 1280×800, document y | **1312px** — **851px** with the prototype chrome (view bar, help line, chips, annotation panel) subtracted |
| Orange CTA top, same basis | 1248px — **787px** without chrome (production capture: y≈791) |
| What is visible at 1280×800 without scrolling | Site header, category nav, breadcrumb, the top of the gallery, and the buy box down to about the quantity tier table. **Neither CTA is above the fold** — the orange CTA is already below it on production, and the quote CTA sits 64px lower. |
| Modal height, 1280×800, breakdown collapsed | **404px** (constraint was ≤526px) |
| Modal height, breakdown expanded | **672px**; with the 64px top offset the modal ends at 736px, so the dialog body still does not scroll |
| Backdrop scrolls at 1280×800 | No. Body `overflow:hidden` while open. |
| Horizontal page overflow at 375 | **0px** on every view — PDP, modal, issued quote, PDF, email |
| Mobile sticky bar, Order button height | 44px |
| Olark widget vs the mobile sticky bar at 375 | No collision (widget bottom 716px, bar top 742px) |
| Olark widget vs the quote CTA at 375 | No collision when the CTA is mid-viewport. The widget is `position:fixed` at 88px from the bottom, so it **can** cover the quote CTA if the CTA comes to rest in the bottom ~130px of the viewport. That is production behaviour, not something this prototype introduces — see Boris asks. |
| Console | Clean. The only entry across the session is a `favicon.ico` 404 from the local dev server. |

## Print verification (computed style, not by eye)

Emulated `media: print`, then read `getComputedStyle().display`:

`none` — action bar, disclosure chevron, orange CTA and its helper, `Calculate now`, delivery form, details link, specialist button, site header, prototype view bar, state chips, annotation panel, mobile sticky bar, Olark widget, cookie banner, PDP view.
`block`/`flex` — the issued quote, the product row body (always expanded), the print-only generation line, the print-only running header, the expired watermark.
Letterhead background computes to `rgba(0,0,0,0)` and the price-table header to `#FFFFFF`, so no fill sits behind body text in print.

## Baymard Compliance

| Rule ID | Rule | Pass/Fail | Notes |
|---|---|---|---|
| B-PDP010 | Primary CTA styling unique on the page | Pass | Orange `#FE5000` 52px fill is used by nothing else on the PDP. The quote CTA is outline-only, 48px, navy. |
| B-PDP013 | Price per unit alongside the total | Pass | `Price each:` in the buy box and the modal; `Average price per unit` under the quote's total; `Price per item` column in the price table. |
| B-PDP014 | Price at least as large as the title | Pass | Quote total 32px/900 vs the H1 at 24px/700. On the PDP the production hierarchy is reproduced unchanged. |
| B-PDP029 | Section titles describe their contents | Pass | "Product and configuration", "Delivery", "Your details", "Your specialist", "Terms". |
| B-PDP038 | Buy section focused | Pass | The sample row moved out of the buy box to the left column, which is what made room for the quote CTA. |
| B-PDP039 | Delivery dates, not speeds | Pass | Every rate row reads "Arrives by Tue, Sep 22, 2026". |
| B-PDP004 | Cross-sells last | N/A | No cross-sell in scope. |
| B-PDP027 / F-PDP008 | No site-initiated overlays | Pass | The modal is click-triggered only. The cookie banner and Olark widget are reproductions of production, and the banner is dismissible. |
| B-PDP041 | Complex customization not a pre-cart form | Pass | The modal edits nothing; `Change` returns to the PDP configurator. |
| B-CHK003 | Every delivery option shows its cost in the selector | Pass | Service, arrives-by and cost on every row. |
| B-CHK005 | No new costs later | Pass | Subtotal, delivery and the tax line are all on the document before the order action. |
| B-CHK008 | Enclosed header | Pass | The quote document runs its own letterhead with no site nav. |
| B-CHK012–015 | Blur validation, red field, adjacent message, data retained | Pass | Both modal fields validate on blur with `aria-invalid` and an adjacent message; Esc and backdrop close without clearing. |
| B-CHK017 | Cheapest option preselected | Pass | FedEx Ground $18.40. |
| B-CHK018 | Actual dates | Pass | As B-PDP039. |
| B-CHK021 | ZIP auto-fills city and state | **Partial** | The address form is pre-filled for the demo; live ZIP lookup is a build-time behaviour, not prototyped. |
| B-CHK022 | Single full-name field | Pass | |
| B-CHK023 | Single phone field, not required | Pass | No phone in the request at all; it is optional inside the details module. |
| B-CHK024 | Address line 2 behind a link | **Deviation** | Omitted rather than hidden — the demo address has no line 2 and the module is already a disclosure inside a disclosure. Flagged for the build. |
| B-CHK025 | Privacy statement near personal data | Pass | Under the two modal fields. |
| B-CHK030 | Primary action more prominent than secondary | Pass | |
| B-CHK031 | Disabled CTA names what is missing; disables on first click | Pass | "Add your name and email to generate the quote." Submit disables itself on click. |
| B-CHK039 | Single-column form | Pass | Both modal fields are in one column; the two-column modal splits *reading* from *entry*, not the form itself. |

## Known deviations from live production

1. **The buy box is legacy Bootstrap, not Tailwind.** The saved DOM shows `btn btn-secondary`, `color-circle-wrapper`, `size-table table-responsive` and `form-control` on this product — the Tailwind rewrite documented in the library for the cotton apron has not reached the tumbler's buy box. The prototype reproduces what the capture renders, not what the library's architecture note says.
2. **Colors is a swatch row, not a read-only recap.** The brief and analysis §6.3 both say a single stock color makes step 1 read-only. The 2026-09-09 capture shows **six swatches** with Blue selected plus a `Blue | 6,765 in stock` line. Built to the capture. Worth reconciling before the analysis is cited again.
3. **Imprint locations use the brief's disambiguated five** (Back, Front (small), Front (large), Two Sided, No Imprint). Production's dropdown carries six entries with duplicate `Back` and `Front` labels distinguished only by size.
4. Header, footer and Q&A are reproductions at component level, not pixel copies: the search box, icon set, Shopper Approved and Trust Guard badges are simplified, and the footer link lists are trimmed.
5. Product images hot-link the production CDN. If that host blocks hot-linking the images will not render on GitHub Pages.
6. The Olark widget is a static placeholder button in the production position, not the real widget.
7. The `View Price Breakdown` link on the PDP raises a toast rather than opening production's `react-modal` dialog. The dialog's content is reproduced faithfully inside the quote modal instead, where it does real work.

## Known deviations from the legacy quote PDF

1. **Navy retinted** `#0F2145` → `#001542`, and **Open Sans replaced by Lato**, so the document and the site stop being two systems.
2. **The price flag is inverted** — total large, per-unit in the caption. When a delivery rate is chosen the caption adds "before delivery", because the flag then holds a figure the per-unit no longer multiplies to.
3. **The disclaimer is replaced, not restyled.** The tariff and market-conditions clause survives as a validity-scoped term: "After Oct 9, 2026, pricing reflects supplier and tariff costs in effect at the time of order." Analysis §6.4 marks this as needing Mike's sign-off.
4. **Mislabelled fields corrected** — `Imprint Area` now holds an area, `Imprint # of Color(s)` is now `Imprint colors`.
5. **Per-page counters are approximate.** The on-screen sheet computes "Page 1 of N" from its own scroll height. Real per-page headers and counters need `@page` margin boxes in the server-side headless print; the prototype's print output carries a single running header instead.
6. The order action is a storefront cart restore, not `admin.promotionpros.com/Invoice/PayInvoice`.

## Decisions made where the analysis was silent

1. **The issued quote opens on a Screen Printed / Back / 1 color / 25 pieces snapshot** — $299.00 base + $31.25 run = **$330.25**, average $13.21. That is production's own captured breakdown, so every price label on the document is checkable against a real capture. The PDP's default state stays Laser Engraved, matching production, which is also what makes the amber "default decoration" notice appear on first open.
2. **Quote number Q-48310**, issued Sep 9 2026, valid through Oct 9 2026, order W-10488. Deliberately not R6's Q-48213, so screenshots from the two prototypes can never be confused.
3. **Delivery rates**: FedEx Ground $18.40 (Tue Sep 22, matching production's own estimate), 2Day $52.90, Standard Overnight $88.15. Three services, not the legacy PDF's seven, and no LTL.
4. **The price table's `Price per item` column is the base item price**, matching the legacy PDF's arithmetic (its `Price/Item` × qty + run + setup = its total). The loaded per-unit lives in the flag caption.
5. **`Imprint area` is carried as a configuration bullet** at `1.75″W × 4.25″H`, because it is the one spec a reader reconstructing the job from the document cannot infer.
6. **The specialist trigger is quantity above the top tier (500)**, since the tumbler has no `callForPricing` method to demonstrate.
7. **Letterhead zone labels are sentence case** ("Prepared for", "Quote"), not the all-caps the legacy PDF uses, per the Phase 4a check banning `text-transform:uppercase`. Production's own all-caps table headers inside the PDP reproduction were left alone — fidelity wins there.
8. **A print-only running header and watermark were added to the quote document itself**, so printing view 3 in the expired state produces a watermarked sheet rather than relying on the view 4 preview.
9. **The tier table keeps its scroll wrapper and cue** from R4, but was resized so it does not trigger at 1280 or 375. It fires only below ~330px of available width.

## Open items

- The sample row's real eligibility threshold is unknown; the eligible state is a demonstration, annotated as such on the page.
- Whether "Colors" renders as swatches or a read-only recap on single-stock-color products — the capture and the analysis disagree (deviation 2 above).
- Per-page PDF headers and counters cannot be verified from a browser print; they need the server-side generator.
- Copy is placeholder-grade throughout. Phase 3 replaces it verbatim from the new deck.

## Boris asks

1. **Configurator URL scheme.** The quote CTA must replay `?c=&s=&im=&l=` plus **quantity** and **imprint-color count**. One canonical serialiser, used by both the CTA and the quote's restore path.
2. **Sample eligibility rule.** What price threshold gates the sample offer, so the row can render conditionally rather than as a demonstration.
3. **Olark widget z-order and offset.** The widget is fixed at the bottom right and can cover the quote CTA on a 375 viewport and the quote page's sticky bar. Confirm whether it can be offset or auto-collapsed on these surfaces.
4. **Rate-empty is real.** Production's rate call returned "No delivery options found" twice during the 2026-09-03 capture. The prototype ships a recovery state for it; the underlying failure still needs a cause.
5. **Empty Description on this product.** The tumbler's Description accordion body is one line repeating the product name — a content-pipeline gap, out of scope here.
6. **Named events**, not text-matched actions: `quote_started`, `quote_generated`, `quote_viewed`, `quote_pdf_downloaded`, `quote_shared`, `quote_ordered`, `quote_specialist_contacted`.
7. **Price-lock semantics.** Persist the full priced breakdown on the quote record and flag the cart as quote-priced through checkout.

## Change Log

- 2026-09-09 Initial build: five views, production-faithful PDP, modal, issued quote, PDF sheet + print stylesheet, email.
- 2026-09-09 Fixed inline-span collapse in the modal product card and price block (name, `Change`, helper and `Price each:` were running together on one line).
- 2026-09-09 Fixed the same class of bug in the issued quote's collapsed product row and in the delivery rate rows.
- 2026-09-09 Letterhead and PDF section labels moved out of all caps, per the Phase 4a style check.
- 2026-09-09 Flag caption reads "before delivery" once a rate is chosen, so the caption and the figure stop implying a false multiplication.
- 2026-09-09 At ≤680px the collapsed product row stacks and the price table becomes label/value rows — analysis §3c's "no horizontal scroll, no hover dependency" for mobile.
- 2026-09-09 Print stylesheet: hid `Calculate now` and the CTA helper, which were leaking into the printed sheet; added a print-only running header and the expired watermark to the quote document itself.
- 2026-09-09 Tier table resized (fixed layout, 320px min) so all five quantity breaks fit the 420px buy box at 1280 without scrolling; the R4 scroll cue is retained for narrower widths.
- 2026-09-09 Cookie banner OK button wired to dismiss.

## See also

`research/reports/2026-09-09-quote-v2-ux-analysis.md` · `docs/2026-09-09-instant-quote-v2-design-plan.md` · `design/library/promotionpros-pdp.md` · `design/prototypes/2026-09-03-quote-flow/` (R6, kept live for comparison)
