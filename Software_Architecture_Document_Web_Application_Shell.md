# Software Architecture Document — Web Application Shell

| Attribute | Value |
|---|---|
| File name | `Software_Architecture_Document_Web_Application_Shell.md` |
| Version | 2.0 |
| Date | 2026-11-04 |
| Owner | Deltra Systems LLC — Software Development |
| Scope | The page shell of every Deltra web application: navigation, layout tiers, htmx behavior, and their extension rules |
| Status | Draft — pending approval |
| Governing standard | Web-App Development Stack, Section 8. Where this document and the Stack disagree, the Stack governs. |
| Implementation | `web/` in `deltra-webapp-template` |

## Document History

| Version | Date | Change |
|---|---|---|
| 1.x | 2026-11-03 | *Software Architecture Document for SPA Template*: a static prototype of the shell (seven complete HTML documents, `style.css`, `spa-shell.js`, htmx 2.0.10) that required no back end |
| 2.0 | 2026-11-04 | Retitled and rewritten for the shell as built into the reference template, rendered once by Go templates. Active navigation by the longest-prefix rule, replacing file-name prefixes and `data-section`; htmx configuration moved to one `htmx-config` meta element; htmx 2.0.11 with its version in the file name; Alpine.js removed (Stack decision SD-15); template components added to the content layer; sign-out added to the navigation; server integration and automated browser checks documented; Section 2 completed as the single inventory of the shell's files |

> **CONFIDENTIAL — Deltra Systems LLC. Internal engineering reference.**

## 1. Purpose

Every Deltra web application uses the same shell: a persistent navigation and page frame, with only the main content replaced as the user moves between pages. This document describes that shell's design, the rules for extending it, and how to test it. The shell is a **standard part** of the software factory: it is built once, in the reference template, and every application inherits it.

The shell is a progressively enhanced multipage application. Every address is a complete HTML page, so ordinary navigation, bookmarks, reloads, and use without JavaScript all work. When htmx is available, navigating fetches that same page, takes only its `#main-view`, and swaps it into place, so the shell stays mounted and the experience feels like a single-page application without a client-side framework.

## 2. Files

| File in the template | Responsibility |
|---|---|
| `web/templates/layout.html` | The shell, defined once: `<head>` with htmx configuration and scripts; skip link; icon sprite; navigation; global error region; `#main-view`; overlay root; request indicator. A plain layout for signed-out pages |
| `web/templates/partials/nav.html` | The navigation entries: links, disclosure groups, and sign-out |
| `web/templates/partials/icons.html` | The SVG icon sprite; each icon is defined once and referenced with `<use>` |
| `web/templates/partials/pagination.html` | Previous and Next paging for lists, preserving search, filters, and sort |
| `web/templates/pages/*.html` | Page content. Each page fills `#main-view` and has one `<h1>`; list pages also define a `results` block for htmx partial updates |
| `web/static/css/style.css` | Tokens, shell layout, navigation, components, device tiers, accessibility |
| `web/static/js/spa-shell.js` | Sidebar state, disclosure groups, active navigation, global errors, htmx lifecycle |
| `web/static/vendor/htmx-2.0.11.min.js` | htmx, self-hosted and pinned |

This table is the single inventory of the shell's files; the template's `web/README.md` points here. No bundler, package manager, or JavaScript framework is used.

## 3. Architecture

```text
Complete HTML document (rendered by html/template from layout.html)
├── <head>: htmx-config meta, style.css, spa-shell.js (not deferred), htmx (deferred)
├── skip link
├── icon sprite (inline <svg> of <symbol> elements)
├── .page-shell
│   ├── .sidenav                    persistent navigation (partials/nav.html)
│   └── .page-main
│       ├── #app-error              persistent global error region (role="alert")
│       └── #main-view              replaceable page content (hx-history-elt)
├── #overlay-root                   persistent overlay mount
└── #navigation-indicator           persistent request indicator
```

The content boundary is:

```html
<div class="main-view" id="main-view" hx-history-elt>
    {{template "content" .}}
</div>
```

Only this element changes during htmx navigation, including Back and Forward. `hx-history-elt` tells htmx that this element, not `<body>`, is what history saves and restores; without it, a Back navigation would replace the whole body and every listener on the shell would be lost.

Signed-out pages (sign-in, and errors before sign-in) use a plain layout with no navigation.

## 4. Navigation model

The navigation landmark carries inherited htmx configuration:

```html
<nav class="sidenav" hx-boost="true" hx-target="#main-view" hx-select="#main-view"
     hx-swap="outerHTML show:top" hx-push-url="true" hx-indicator="#navigation-indicator">
```

1. A navigation link points to a complete page.
2. Without htmx, the browser navigates normally.
3. With htmx, the page is requested in the background, `hx-select` takes its `#main-view`, the current `#main-view` is replaced, and the real address is pushed onto browser history.

The sign-out form inside the navigation sets `hx-boost="false"`: signing out leaves the shell for the signed-out layout, so it must be an ordinary request.

Links and forms inside `#main-view` are ordinary, unless a page deliberately adds htmx attributes. The standard example is a list page, whose search form and paging links swap only a `#results` region (Section 13).

## 5. HTML responsibilities

- `<nav>` is the primary navigation landmark.
- Real destinations use `<a href>`. Disclosure controls use `<button>`.
- Unavailable destinations are non-interactive elements with `aria-disabled="true"`, never placeholder `href="#"` links.
- Every page has one `<h1>`, inside `#main-view`.
- A skip link lets keyboard users bypass the navigation.
- Decorative SVG uses `aria-hidden="true"`. All icon geometry is in the sprite.
- Top-level navigation controls have accessible names and `title` text, so their names survive the collapsed and phone layouts where labels are hidden.
- No inline scripts, inline styles, or inline event handlers (Content Security Policy, Stack Section 10.6).

## 6. State ownership

### 6.1 Sidebar state

The root attribute is authoritative: `<html data-sidebar="expanded">` or `"collapsed"`. CSS reads it directly.

`spa-shell.js` stores the user's choice in `localStorage` under `deltra.sidebarCollapsed` (Stack decision SD-17). It is a per-browser convenience; storage failure is not an error. With no stored choice, the sidebar starts collapsed below 1024px and expanded at 1024px and above. The phone layout never writes the preference.

`spa-shell.js` is loaded in `<head>` without `defer`, so it applies this state before the body paints. Code that needs body elements waits for `DOMContentLoaded`.

### 6.2 Disclosure state

`aria-expanded` on the button is the authoritative open state. The controlled submenu uses the native `hidden` attribute:

```html
<button class="nav-toggle" aria-controls="submenu-admin" aria-expanded="false">…</button>
<ul class="submenu" id="submenu-admin" hidden>…</ul>
```

JavaScript changes only those two attributes. CSS derives color and arrow rotation from `[aria-expanded="true"]`. There are no duplicate state classes.

### 6.3 Active navigation

The active entry is marked by JavaScript, never by the server (Stack decision SD-16), because the navigation persists across htmx swaps and only JavaScript sees every change.

**Rule:** the navigation link whose address is the longest prefix of the current address is active. `/` matches only the home page; every other link matches its own address and every address below it. On `/items/7/edit`, the link to `/items` is active.

The active link receives `class="nav-link is-active"` and `aria-current="page"`. Each disclosure group whose submenu contains the active link receives `has-active-child` and, except on phones, opens. Group membership is read from the markup (which submenu contains the link), so no route metadata is duplicated.

## 7. JavaScript responsibilities

`spa-shell.js` is an immediately invoked function with no globals. It:

- applies and persists sidebar state, and keeps the sidebar button's label and `aria-expanded` current;
- opens and closes disclosure groups;
- marks the active navigation entry (Section 6.3);
- shows global request errors and clears them after the next successful navigation;
- after each `#main-view` swap or history restore, re-marks the navigation, closes any phone sheet, and moves focus to the new `<h1>`; and
- on phones, presents an open top-level group as a sheet above the tab bar, one at a time, closed by Escape, an outside tap, or navigation.

All listeners are delegated from `document` and registered once, so the shell keeps working even if part of it is re-rendered.

The shell file holds no page-specific behavior. Page behavior goes in a small ES module in `web/static/js/`. Alpine.js is not used (Stack decision SD-15).

## 8. htmx configuration and lifecycle

### 8.1 Configuration

htmx is configured once, in the layout:

```html
<meta name="htmx-config" content='{"historyCacheSize": 0, "includeIndicatorStyles": false,
                                   "allowEval": false, "allowScriptTags": false}'>
```

- `historyCacheSize: 0` — htmx 2 normally keeps copies of visited pages in `sessionStorage` for Back and Forward. Deltra pages show confidential client data, so none are kept: Back re-requests the page, and htmx swaps its `#main-view`. htmx still records the current address (`htmx-current-path-for-history`), which holds no page content.
- `includeIndicatorStyles: false` — htmx would otherwise inject a `<style>` element, which the Content Security Policy blocks. The request indicator is styled in `style.css`.
- `allowEval: false`, `allowScriptTags: false` — no evaluated code and no scripts executed from swapped content.

htmx 2.0.11 is self-hosted with its version in the file name. Upgrade it deliberately, update this document, and test Section 12. htmx 4 changes event names, history behavior, and attribute inheritance, and it swaps 4xx and 5xx responses by default; moving to it means revisiting `wireHtmx()` in `spa-shell.js`.

### 8.2 Lifecycle contract

After a successful `#main-view` swap, and after `htmx:historyRestore`, the shell:

1. hides any global error left from an earlier request;
2. re-marks the active navigation;
3. closes any open phone sheet; and
4. gives the new `<h1>` `tabindex="-1"` and moves focus to it, so screen-reader users hear the new page.

htmx sets `document.title` from the fetched page's `<title>`.

The shell also handles `htmx:responseError` (HTTP failures, with plain messages for 401 and 403), `htmx:sendError` (connection failures), and `popstate`.

## 9. CSS architecture

The stylesheet declares its cascade layers once, in priority order:

```css
@layer reset, tokens, shell, nav, content, device, a11y;
```

A rule in a later layer beats a rule in an earlier layer regardless of selector specificity, so device rules override navigation rules without `!important`. The one `!important` is the `[hidden]` rule in the reset layer, which therefore outranks every later layer.

Custom properties in the `tokens` layer hold every reusable value: colors, spacing, radii, navigation widths, focus treatment, and transitions. Reduced motion sets the transition tokens to `0s`.

The `content` layer holds page content: the main view, cards, the error banner, and the **components** used by pages (buttons, forms and fields, filters, data tables, pagination, record details, notices). Because `content` comes after `nav`, rules in it must be class-based or scoped to `.main-view`; a bare element selector there would restyle the navigation.

### 9.1 Container queries

`.main-view` is a named inline-size container. Components respond to the width of the content region, including the space gained when the sidebar collapses.

### 9.2 Device tiers

Viewport media queries choose among three width ranges:

| Tier | Width | Examples (CSS pixels, portrait) | Navigation |
|---|---|---|---|
| Phone | under 600px | iPhone 17 (402), 17 Pro Max (440) | Bottom tab bar; groups open as a sheet above it |
| Tablet | 600–1023px | iPad mini (744), iPad (820), iPad Pro 11" (834), phones in landscape | Sidebar, collapsed by default |
| Desktop | 1024px and up | iPad Pro 13" (1032), laptops and monitors | Sidebar, expanded by default |

The 600px value appears in `style.css` and as `PHONE_LAYOUT` in `spa-shell.js`; change both together. Content stops widening at `--content-max`. The viewport uses `viewport-fit=cover`, and the shell pads itself with `env(safe-area-inset-*)`.

## 10. Accessibility contract

Extensions must preserve these:

- Every interactive control has an accessible name at every tier.
- Keyboard focus is visible (`:focus-visible`).
- Links navigate; buttons change interface state.
- Disclosure buttons keep `aria-controls` and an accurate `aria-expanded`.
- Only the active entry has `aria-current="page"`.
- Every page has a meaningful `<h1>`.
- Request failures are announced through the persistent `role="alert"` region.
- The request indicator is visual only (`aria-hidden="true"`).
- Motion respects `prefers-reduced-motion`; forced-colors mode keeps the active marker visible.
- Unavailable destinations are not fake links.

If the navigation grows substantially, design a deliberate phone layout (such as a drawer) rather than stacking more bottom-bar groups.

## 11. Adding a page

1. Add the page template in `web/templates/pages/`, defining `content` with one `<h1>`.
2. Add the handler and route (Stack Section 7), protected as the SRS requires.
3. If the page belongs in the navigation, add a link in `web/templates/partials/nav.html`, using only its `href`. Do not mark it active; the shell does.
4. If the page is below an existing entry's address (for example `/items/7` below `/items`), nothing more is needed: the longest-prefix rule marks its parent entry.
5. Test it with ordinary navigation and with htmx navigation (Section 12).

## 12. Testing

Run the application locally (`make run`) and test:

- ordinary navigation with JavaScript disabled;
- htmx navigation from the navigation, and its address, title, and focus changes;
- the active entry on deep addresses (for example `/items/7/edit` marks Items);
- Back, Forward, reload, and copied addresses;
- after several navigations and Back, the sidebar toggle and disclosure buttons still respond;
- `sessionStorage` holds no `htmx-history-cache` entry;
- sidebar collapse and its persistence across reloads;
- phone sheets: one open at a time, closed by Escape, an outside tap, and navigation;
- the three tiers at 402, 744, 820, 1032, 1366, and 1920 pixels wide;
- no console errors with the production Content Security Policy;
- visible keyboard focus and the skip link;
- container-query reflow of components; and
- network and HTTP error messages.

Several of these checks were run automatically in a headless browser when this version was prepared. The reference template should keep them as an automated browser test as its tooling grows.

## 13. Server integration

- **Full pages always work.** Handlers render the complete layout. htmx navigation takes `#main-view` from it, so handlers need no special case for navigation.
- **Partial responses are optional and explicit.** A list page may return only its `results` block when the request's `HX-Target` is `results`. Any handler that returns different content for htmx requests sends `Vary: HX-Request`, so a browser or proxy cache never serves a fragment for an ordinary page load.
- **History restores get full pages.** With history caching off, Back sends `HX-History-Restore-Request`; the handler returns the complete page and htmx extracts `#main-view`.
- **Error responses are not swapped.** htmx 2 does not swap 4xx and 5xx responses; the shell shows a message instead. Forms that report validation errors with HTTP 422 are therefore ordinary form submissions, and render the full page with the errors.
- **Expired sessions.** For an htmx request without a valid session, the server responds 401 with an `HX-Redirect` header to the sign-in page.
- The server remains responsible for authentication, authorization, input validation, CSRF protection, database work, and escaping user-controlled content (Stack Section 10).

## 14. Architectural guardrails

- Start with complete semantic HTML and enhance it.
- Keep the shell independent of page-specific business logic.
- Keep addresses bookmarkable and meaningful.
- Do not duplicate link destinations into route metadata; the `href` is the source of truth.
- Do not create several CSS classes for one disclosure state.
- Register global listeners once, delegated from `document`.
- Use container queries for component space and media queries for viewport decisions.
- Add a dependency only when browser features, htmx, and small local scripts are insufficient (Stack Section 5).
- Measure real performance before adding architectural complexity.

## 15. Summary

Complete documents supply resilience; htmx supplies partial navigation; a small vanilla JavaScript file manages the persistent shell; and CSS adapts both to the content width and the device. The shell is built once in the reference template and inherited by every application.

---
*End of Document*
