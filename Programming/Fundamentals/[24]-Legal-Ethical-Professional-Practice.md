[Previous](./[23]-Supporting-Math-Foundations-optional.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[25]-Best-Practices.md)

# Lesson 24 - Legal, Ethical & Professional Practice

> **Note:** This section provides general, educational information about legal and professional concepts relevant to software development. It is not legal advice. Specific licensing, IP, or compliance questions should be directed to a qualified attorney, especially for decisions with real commercial or legal consequences.

## 24.1 Licensing (Open Source vs. Proprietary)

A software **license** is a legal document defining how software can be used, modified, and distributed. Understanding licensing matters both for consuming other people's code and for deciding how to license your own.

### Proprietary (Closed-Source) Licensing

The source code is not publicly available, and usage is restricted by a license agreement (often an EULA — End User License Agreement) that typically prohibits copying, modifying, reverse-engineering, or redistributing the software. The vendor retains full control over the codebase. Examples: most commercial desktop/enterprise software, many SaaS products.

### Open Source Licensing

Open source licenses grant users the right to view, use, modify, and often redistribute the source code, but the *specific* permissions and obligations vary significantly by license. Open source licenses broadly fall into two families:

**Permissive licenses** — impose minimal restrictions; code can generally be reused in proprietary projects with few obligations (typically just preserving the copyright notice).

| License | Key Characteristics |
|---|---|
| **MIT** | Extremely permissive; allows almost any use, including in proprietary software, with just attribution required |
| **Apache 2.0** | Similar to MIT, but adds an explicit patent grant (protecting users from patent claims by contributors) and requires stating changes made to the code |
| **BSD (2-clause / 3-clause)** | Similar to MIT; the 3-clause variant adds a restriction against using contributors' names for endorsement |

**Copyleft licenses** — require that derivative works be distributed under the same (or a compatible) license, to keep the code and its derivatives open.

| License | Key Characteristics |
|---|---|
| **GPL (GNU General Public License)** | "Strong copyleft" — any derivative work that is distributed must also be released under the GPL, including the full source code |
| **LGPL (Lesser GPL)** | "Weak copyleft" — allows linking to LGPL code from proprietary software, but modifications to the LGPL-licensed code itself must remain LGPL |
| **AGPL (Affero GPL)** | Extends GPL's copyleft to network use — if you run modified AGPL code as a service (even without distributing the binary), you must still make the source available to users of that service |

### Permissive vs. Copyleft — Practical Implications

```
Permissive (MIT, Apache, BSD):
  Can use in proprietary/closed products?  YES
  Must share your own modifications?        NO (but attribution required)

Copyleft (GPL):
  Can use in proprietary/closed products?   Generally NO (if distributed)
  Must share your own modifications?         YES, under the same license
```

This distinction has real consequences for businesses: a company building a proprietary product must be careful about which open-source dependencies it includes, since incorporating GPL-licensed code into a distributed product can create an obligation to open-source the entire combined work — a serious business/legal consideration, not a minor technicality.

### Choosing a License for Your Own Project

Common considerations:
- **Want maximum adoption, even in proprietary contexts?** → Permissive (MIT, Apache 2.0).
- **Want to ensure derivatives always remain open?** → Copyleft (GPL family).
- **Building something offered as a network service, worried about others offering it as a service without contributing back?** → AGPL specifically addresses the "SaaS loophole" that plain GPL doesn't cover.
- **Need explicit patent protection?** → Apache 2.0 includes an express patent grant that MIT/BSD lack.

### Practical Best Practices

- **Always check the license before using a third-party library**, especially in commercial products — don't assume "free to download" means "free to use however you want."
- **Track dependency licenses** — tools like `license-checker` (npm), `pip-licenses` (Python), or dedicated software composition analysis (SCA) tools can audit an entire dependency tree for license compliance issues.
- **License compatibility matters** — combining code under incompatible licenses (e.g., some copyleft variants aren't compatible with each other) can create legal complications; when in doubt, consult legal counsel for anything commercially significant.
- **"No license" does not mean "public domain"** — code without an explicit license is, by default, **fully copyright-protected** with no usage rights granted; the absence of a license file is far more restrictive than people often assume.

---

## 24.2 Intellectual Property Basics

Intellectual property (IP) law protects creations of the mind. Several distinct IP categories are relevant to software.

### Copyright

Protects **original works of authorship** — including source code, documentation, user interface designs, and creative content (text, images, music) — automatically, from the moment of creation, without requiring registration (though registration provides additional legal benefits in many jurisdictions, such as the ability to sue for statutory damages in the US).

- **What it protects:** the specific *expression* of an idea (the actual code/text/design), not the underlying idea, algorithm, or functionality itself.
- **What it doesn't protect:** facts, ideas, methods, or algorithms in the abstract — only their specific, original expression.
- **Duration:** typically the author's lifetime plus several decades (varies by jurisdiction), though corporate/work-for-hire copyrights often have a different fixed term.
- **Relevance to developers:** copying code verbatim from another project (without a compatible license or permission) is a copyright concern — this is a large part of *why* open source licenses exist, to explicitly grant permissions that would otherwise not exist by default.

### Patents

Protect **inventions** — new, useful, and non-obvious processes, machines, or methods — for a limited period (commonly 20 years from filing), granted only after a formal application and examination process.

- **Software patents** are legally controversial and vary significantly by jurisdiction — some countries grant them fairly broadly (notably the US, under certain conditions), while others (e.g., much of the EU) restrict patenting "software as such," generally requiring a technical effect beyond just the algorithm/logic itself.
- Unlike copyright, a patent can protect the underlying *idea/method* itself, not just a particular expression of it — a much broader (and more expensive/difficult to obtain) form of protection.
- **Practical relevance:** developers should generally be aware that implementing a well-known algorithm is usually safe, but implementing something functionally very close to a company's patented, novel process could pose legal risk in patent-friendly jurisdictions — this becomes especially relevant in specialized domains (codecs, cryptographic techniques, certain UI interaction patterns) where patents are more common.

### Trademarks

Protect **brand identifiers** — names, logos, slogans — used to distinguish goods/services from competitors.

- **Relevance to developers:** naming a project, product, or company something confusingly similar to an existing trademark (especially in the same industry) can create legal exposure, independent of any copyright/patent concerns.
- Trademarks are tied to the *specific context* of use (e.g., "Delta" as an airline vs. "Delta" as a faucet brand can coexist, since they're unrelated industries and unlikely to cause consumer confusion).

### Trade Secrets

Confidential business information (algorithms, processes, formulas, internal tools) that provides a competitive advantage, protected not through registration but through active efforts to keep it secret (e.g., NDAs, access controls). Unlike patents, trade secret protection can last indefinitely — but only as long as the information actually remains secret; once disclosed, the protection is generally lost permanently.

### IP Considerations in Everyday Development

- **Employee/contractor agreements** — most employment contracts include IP assignment clauses specifying that code written on the job belongs to the employer, not the individual developer — worth understanding, especially for any side projects done using company time/resources/tools.
- **Using AI-generated code** — the copyright status of AI-generated code is a legally evolving and, as of this writing, not fully settled area across jurisdictions; teams should be aware this is an active area of legal development and may want specific guidance depending on jurisdiction and use case.
- **Contributing to open source** — contributing code to a project typically requires agreeing to license your contribution under that project's chosen license (sometimes formalized via a Contributor License Agreement, or CLA).
- **Attribution requirements** — even permissive licenses like MIT require preserving the original copyright/license notice — a commonly overlooked but real legal obligation, not just a courtesy.

---

## 24.3 Accessibility (a11y) Basics

Accessibility (often abbreviated **a11y** — "a," 11 letters, "y") is the practice of designing and building software usable by people with a wide range of abilities and disabilities, including visual, auditory, motor, and cognitive impairments.

### Why Accessibility Matters

- **It's the right thing to do** — technology should be usable by everyone, not just people without disabilities.
- **Legal requirements** — many jurisdictions have legal accessibility requirements for certain classes of software/websites (e.g., the Americans with Disabilities Act (ADA) in the US, the European Accessibility Act, Section 508 for US federal agencies), and non-compliance can carry real legal and financial risk.
- **Broader benefits** — accessible design frequently improves usability for *everyone*, not just people with disabilities (e.g., captions help in noisy environments; good keyboard navigation helps power users; clear color contrast helps in bright sunlight).
- **Larger addressable audience** — a meaningful percentage of any user base has some form of disability, whether visible or not, and accessible design directly expands who can use a product.

### The WCAG Framework

The **Web Content Accessibility Guidelines (WCAG)**, published by the W3C, are the internationally recognized standard for web accessibility, organized around four principles — **POUR**:

| Principle | Meaning |
|---|---|
| **Perceivable** | Information must be presentable in ways users can perceive (e.g., text alternatives for images, captions for video) |
| **Operable** | Interface components must be operable by all users (e.g., full keyboard navigability, no content that triggers seizures) |
| **Understandable** | Information and UI operation must be understandable (clear language, predictable navigation, helpful error messages) |
| **Robust** | Content must work reliably across a wide variety of user agents, including assistive technologies |

WCAG defines three conformance levels (**A**, **AA**, **AAA**, in increasing strictness); **AA** is the most commonly required target in legal/regulatory contexts.

### Common Accessibility Practices

**Semantic HTML** — using HTML elements according to their intended meaning (not just visual appearance), which assistive technologies rely on to interpret page structure correctly.
```html
<!-- Poor: a div styled to look like a button conveys no semantic meaning -->
<div class="button" onclick="submit()">Submit</div>

<!-- Better: a real button is natively keyboard-accessible and announced correctly by screen readers -->
<button onclick="submit()">Submit</button>
```

**Alt text for images** — describing image content for users relying on screen readers.
```html
<img src="chart.png" alt="Bar chart showing revenue growth of 20% in Q3 2026">
<!-- Purely decorative images should use empty alt="" so screen readers skip them -->
<img src="decorative-swirl.png" alt="">
```

**Keyboard navigation** — ensuring all interactive elements (buttons, forms, menus) are fully usable without a mouse, a requirement both for motor-impaired users and for screen reader users generally.

**Color contrast** — sufficient contrast between text and background so content remains legible for users with low vision or color blindness (WCAG AA generally requires a contrast ratio of at least 4.5:1 for normal text).

**Never rely on color alone** to convey information (e.g., "required fields are shown in red" excludes colorblind users — pair color with text/icons as well).
```html
<!-- Poor: relies solely on color to indicate an error -->
<input style="border-color: red">

<!-- Better: color plus an explicit text label/icon -->
<input aria-invalid="true" aria-describedby="email-error">
<span id="email-error">⚠ Please enter a valid email address</span>
```

**ARIA (Accessible Rich Internet Applications) attributes** — supplement HTML semantics when native elements aren't sufficient (particularly for complex, custom interactive widgets), providing information to assistive technologies about roles, states, and properties.
```html
<div role="alert" aria-live="polite">Your changes have been saved.</div>
```
*Rule of thumb: prefer native semantic HTML elements first; use ARIA only to fill genuine gaps, since incorrect ARIA usage can make accessibility worse, not better.*

**Captions and transcripts** — for audio/video content, benefiting deaf/hard-of-hearing users as well as anyone in a sound-off context.

**Form labels** — every input should have a properly associated, visible label, not just placeholder text (which disappears once the user starts typing and isn't reliably announced by all screen readers).
```html
<label for="email">Email address</label>
<input id="email" type="email">
```

### Testing for Accessibility

- **Automated tools** (axe, Lighthouse, WAVE) catch a meaningful subset of issues (missing alt text, insufficient contrast, missing form labels) but cannot catch everything — many accessibility problems are contextual and require human judgment.
- **Manual testing:** navigating the interface using only a keyboard (Tab, Enter, arrow keys, no mouse), and testing with an actual screen reader (VoiceOver on macOS/iOS, NVDA or JAWS on Windows, TalkBack on Android).
- **Involve users with disabilities** in testing where possible — the most reliable way to surface real usability issues that automated tools and simulated testing miss.

---

## 24.4 Internationalization/Localization (i18n)

**Internationalization** (often abbreviated **i18n** — "i," 18 letters, "n") is the process of designing software so it *can* be adapted to different languages, regions, and cultural conventions. **Localization** (**l10n** — "l," 10 letters, "n") is the actual process of adapting the software for a *specific* locale — translating text, adjusting formats, and adapting cultural conventions.

```
Internationalization (i18n): building the CAPABILITY to support multiple locales
Localization (l10n):          actually ADAPTING the product for a specific locale
```

### Why It Matters

Products built without internationalization in mind from the start are often expensive and error-prone to retrofit later — text may be hardcoded throughout the codebase, date/number formats may be hardcoded to one convention, and UI layouts may not accommodate languages with very different text lengths or reading directions.

### Text Externalization

The foundational i18n practice: never hardcode user-facing text directly in code; instead, store it in external resource files, referenced by key, so translations can be added without touching the application logic.

```javascript
// Poor: hardcoded text, impossible to translate without modifying code
function greet(name) {
  return `Hello, ${name}! Welcome back.`;
}

// Better: externalized, with locale-specific resource files
// en.json: { "greeting": "Hello, {name}! Welcome back." }
// es.json: { "greeting": "¡Hola, {name}! Bienvenido de nuevo." }
function greet(name, locale) {
  return t("greeting", { name }, locale);   // 't' = translation function
}
```

### Date, Time, and Number Formatting

Conventions vary significantly across locales and must never be hardcoded:

| Element | US (en-US) | Germany (de-DE) | Example |
|---|---|---|---|
| Date | MM/DD/YYYY | DD.MM.YYYY | 07/18/2026 vs. 18.07.2026 |
| Decimal separator | `.` | `,` | 1,234.56 vs. 1.234,56 |
| Currency | `$1,234.56` | `1.234,56 €` | Symbol position and separators differ |
| First day of week | Sunday | Monday | Affects calendar UI layout |

```javascript
// Use locale-aware formatting APIs rather than manual string formatting
new Intl.DateTimeFormat("de-DE").format(new Date());
new Intl.NumberFormat("de-DE", { style: "currency", currency: "EUR" }).format(1234.56);
```

**Time zones** deserve particular care: always store timestamps in a standard, unambiguous format (typically UTC) and convert to the user's local time zone only at the point of display — storing "local time" directly leads to a wide range of subtle, hard-to-diagnose bugs (especially around daylight saving time transitions).

### Text Length and Layout

Translated text can be significantly longer or shorter than the original (German text, for example, is often notably longer than the equivalent English) — UI layouts must be flexible enough to accommodate this variation rather than assuming a fixed text length, or text will visibly overflow, truncate, or wrap awkwardly.

### Right-to-Left (RTL) Language Support

Languages like Arabic and Hebrew are read right-to-left, requiring **mirrored layouts** — not just flipped text direction, but often the entire UI (navigation, icons implying directionality, form alignment) mirrored horizontally.
```css
/* CSS logical properties adapt automatically to text direction,
   instead of hardcoding assumptions about left/right */
.container {
  margin-inline-start: 16px;  /* adapts to LTR or RTL automatically */
  /* rather than: margin-left: 16px; (which breaks in RTL contexts) */
}
```

### Pluralization

Different languages have different, sometimes far more complex, pluralization rules than English's simple singular/plural distinction (some languages have three, four, or more grammatical plural forms depending on the exact quantity) — i18n libraries typically provide dedicated pluralization handling rather than relying on simple string concatenation.
```javascript
// Naive approach breaks for many languages
`${count} item${count !== 1 ? 's' : ''}`

// i18n libraries handle locale-specific plural rules correctly
i18n.t("item_count", { count });
```

### Character Encoding

Always use **Unicode (UTF-8)** consistently throughout an application — from database storage, to file I/O, to HTTP responses — to correctly support the full range of characters across all supported languages (accented characters, non-Latin scripts, emoji). Inconsistent encoding is a common source of "mojibake" (garbled, incorrectly decoded text).

### Cultural Considerations Beyond Language

- **Colors and imagery** can carry different cultural meanings/connotations across regions (e.g., colors associated with mourning vary significantly by culture).
- **Address formats, name formats, and phone number formats** vary substantially by country — avoid assuming a single "first name / last name" structure or a single postal address format applies universally.
- **Legal and regulatory differences** — data privacy laws (like GDPR in the EU), content restrictions, and required disclosures can vary meaningfully by region and may require region-specific product behavior.

### i18n/l10n Best Practices

- **Design for internationalization from the start** — retrofitting i18n into a codebase with pervasive hardcoded text and formatting is far more costly than building it in from the beginning.
- **Never concatenate translated string fragments** — sentence structure and word order vary by language, so concatenating pieces (`"You have " + count + " new messages"`) frequently produces grammatically incorrect or nonsensical translations; use full parameterized template strings instead.
- **Test with pseudo-localization** — a technique that expands and alters visible strings (e.g., adding padding characters, accenting characters) to catch layout/text-length issues even before real translations are available.
- **Separate translation work from code changes** — use a dedicated translation management workflow/platform so translators (often non-engineers) can work independently of the engineering release cycle.
- **Account for text expansion in UI design** — leave sufficient flexible space in layouts rather than designing tightly around the original language's text length.

[Previous](./[23]-Supporting-Math-Foundations-optional.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[25]-Best-Practices.md)
