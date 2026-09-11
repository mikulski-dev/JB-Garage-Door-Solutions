# Website

Static marketing site for JB Garage Door Solution, a garage door sales, installation and repair company serving Mesquite, New Mexico.

> **Status: content conversion complete.** Every page now describes the new owner and trade. What
> remains is real-world material: photography, a logo, price bands, testimonials, the enquiry-form
> endpoint and a confirmed domain. See the checklist below.

---

## Stack

Plain HTML + Tailwind (browser CDN build) + a small `global.js`. No build step, no dependencies.
Deployed to Vercel.

`global.js` fetches `components/header.html` and `components/footer.html` at runtime and injects
them into the `#header-placeholder` / `#footer-placeholder` divs on every page — so **the nav is
edited in one place only**.

Because the components are fetched over HTTP, opening a page with `file://` renders it without the
header and footer. Serve locally instead:

```
npx serve .
```

---

## Structure

```
index.html                  Home
style.css                   Shared styles (buttons, reveal animations, underlines)
global.js                   Component injection, mobile nav, scroll reveal, header scroll
components/
  header.html               Top bar, logo, mega menu, mobile drawer
  footer.html               Contact block, quick links, credits
pages/
  about.html                Founder story, commitments, standards
  services.html             Service catalogue grid
  gallery.html              Filterable project gallery
  reviews.html              Client reviews (see note below)
  contact.html              Enquiry form, service-area map, FAQ
  privacy-policy.html
  thank-you.html            Form success page (noindex via robots.txt)
  services/                 One page per service (9): loft-conversions, extensions,
                            kitchen-refurbishment, full-refurbishment, new-build,
                            office-fit-out, restaurant-fit-out, drone-surveys,
                            snagging-inspections
assets/imgs/
  service_imgs/<Service>/   Per-service photo folders
  hero_slider/              Home hero slideshow images
sitemap.xml, robots.txt     SEO
site.webmanifest            PWA manifest + icon references
_headers, vercel.json       Security headers / CSP (keep the two in sync)
```

---

## Brand

| Token | Value |
|---|---|
| Primary / dark | `#000000` |
| Accent (timber) | `#C98756` |
| Secondary (green) | `#0D7F15` |
| Light background | `#F5F3EE` |
| Headings | Playfair Display |
| Body | DM Sans |

Colours are declared in a Tailwind `@theme` block that is **duplicated inline in every HTML file**
(`--color-brand-dark`, `--color-brand-bronze`, `--color-brand-green`, …). Change one, change all —
a scripted find/replace is the practical way to do it. `site.webmanifest` carries `theme_color` and
`background_color` separately.

---

## Contact form

Both `index.html` and `pages/contact.html` POST to a Google Apps Script web app via a `GAS_URL`
constant.

**`GAS_URL` is currently the placeholder `REPLACE_WITH_NEW_APPS_SCRIPT_EXEC_URL`, so the form does
not submit.** To activate it, create a Google Sheet + Apps Script web app on the site owner's own
Google account and paste its `/exec` URL into both files.

Apps Script to deploy (Extensions → Apps Script on a new Sheet):

```javascript
function doPost(e) {
  const data = JSON.parse(e.postData.contents);
  const sheet = SpreadsheetApp.openById('YOUR_SHEET_ID').getActiveSheet();
  sheet.appendRow([
    new Date(),
    data.name    || '',
    data.email   || '',
    data.phone   || '',
    data.service || '',
    data.message || '',
    data.source  || ''
  ]);
  return ContentService.createTextOutput('OK');
}
```

Deploy → New deployment → Web app → Execute as **Me**, Who has access **Anyone**. Copy the `/exec`
URL into `GAS_URL` in both files.

⚠️ **CSP.** `script.google.com` has been removed from `script-src`, `connect-src` and `form-action`
in both `_headers` and `vercel.json`. If you wire the form back up to Apps Script you must re-add
`https://script.google.com` (and `https://script.googleusercontent.com` for `_headers`) to those
three directives, or the POST will be blocked in the browser.

---

## Reviews

`pages/reviews.html` ships with **no testimonials**. It contains a commented `review-card`
template — paste one block per genuine review, then delete the `#no-reviews-yet` block. Only
publish reviews the current owner has actually received; never carry over copy written by another
business's customers.

---

## Social links

There are currently **no social profiles** for this site. The footer social cluster, the
`sameAs` schema entry on the home page, and the Facebook review button on `pages/reviews.html`
have all been removed. Facebook links do still remain in `components/header.html` and
`pages/contact.html` and need removing or repointing.

---

## Images

All photography in `assets/imgs/` is inherited placeholder material. Everything needs replacing,
along with the favicons (`favicon*.png`, `favicon.ico`, `apple-touch-icon.png`,
`android-chrome-*.png`), `assets/imgs/logo.png`, `logo-hovered.png` and `assets/imgs/og-image.jpg`
(1200×630).

Service pages point at `assets/imgs/service_imgs/<Service>/` — drop new photos into the matching
folder and update the `<img src>` list in each page. Several service folders are currently empty.

**Placeholders in use.** The favicons (`favicon.ico`, `favicon-16x16.png`, `favicon-32x32.png`,
`apple-touch-icon.png`, `android-chrome-192x192.png`, `android-chrome-512x512.png`) and the five
`assets/imgs/hero_slider/slide1–5` images are generated brand-neutral placeholders, not client
assets — the hero slides carry a visible "PLACEHOLDER" label so they cannot ship unnoticed. Replace
all eleven before launch.

Every local `src` / `href` / `url()` reference in the site currently resolves on disk — nothing 404s.

---

## Rebrand checklist

Done:

- [x] Business name, owner name, phone, email across all pages and both components
- [x] Domain, canonicals, `og:url`, sitemap, robots, JSON-LD `url`/`@id`
- [x] Schema — `areaServed` = Mesquite, NM, all blocks re-validated
- [x] Geography in all copy, titles, meta and the contact-page area ticker
- [x] All social markup removed — the site has no Facebook links anywhere
- [x] Nine service pages renamed and repopulated from section 5 of the content doc
- [x] Home page fully converted; Reviews nav item and before/after slider removed
- [x] `about.html`, `gallery.html` (now Capabilities) and `contact.html` converted
- [x] Previous client's photography and logo removed from `assets/imgs/` entirely
- [x] Every unverified claim removed — waste-carrier licence, sustainably sourced timber, garden
      clearance, insurance, and all "real completed client work" provenance claims

Outstanding — needs material from the client:

- [ ] **Photography.** Every image in `assets/imgs/` is a generated `PLACEHOLDER`. Nothing on the
      site shows real work.
- [ ] **Logo.** `logo.png` / `logo-hovered.png` are placeholder wordmarks. Favicons likewise.
- [ ] **OG share image.** `og-image.jpg` is a placeholder (correct 1200x630 dimensions).
- [ ] **Estimate / cost-guide page** — section 6 of the content doc; blocked on price bands.
- [ ] **Testimonials** — `pages/reviews.html` has none and is unlinked from the nav; decide whether
      to delete the page and its sitemap entry, or populate it.
- [ ] `GAS_URL` on both form pages, plus the CSP note above.
- [ ] Confirm the domain — `jbgaragedoorsolution.com` should be verified before launch.
- [ ] Address and postcode for the footer, if the owner wants one published.
- [ ] Palette — still the inherited black/bronze/green; the content doc specifies black/white.

## Deployment

Vercel, static, no build command. `vercel.json` sets the security headers and CSP for Vercel;
`_headers` does the same for Netlify-style hosts — **keep both in sync**. `sitemap.xml` and
`robots.txt` still reference the old domain and must be updated at cutover.
