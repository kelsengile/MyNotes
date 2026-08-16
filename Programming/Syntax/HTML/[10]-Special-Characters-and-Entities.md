[Previous](./[9]-Comments-and-Whitespace.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[11]-Why-Semantics-Matter.md)

*Text & Content Basics*

# Lesson 10 - Special Characters & HTML Entities

## 10.1 Why Entities Exist

Some characters have special meaning in HTML — like `<` and `>`, which define tags — so you can't type them directly as text without confusing the browser. **HTML entities** let you display these reserved characters safely.

```html
<p>5 &lt; 10 and 10 &gt; 5</p>
```

Without the entities, `<10` would look like the start of a new tag to the browser.

---

## 10.2 Common Entities

| Character | Entity Name | Entity Number |
|---|---|---|
| `<` | `&lt;` | `&#60;` |
| `>` | `&gt;` | `&#62;` |
| `&` | `&amp;` | `&#38;` |
| `"` | `&quot;` | `&#34;` |
| `'` | `&apos;` | `&#39;` |
| (non-breaking space) | `&nbsp;` | `&#160;` |
| © | `&copy;` | `&#169;` |
| € | `&euro;` | `&#8364;` |

```html
<p>Ben &amp; Jerry&rsquo;s &copy; 2026</p>
```

---

## 10.3 Numeric Character References

Every character has a numeric code point you can reference with `&#NUMBER;` (decimal) or `&#xHEX;` (hexadecimal) — useful for symbols that don't have a friendly named entity.

```html
<p>Heart symbol: &#9829;</p>
<p>Same symbol in hex: &#x2665;</p>
```

As a rule of thumb: always escape `<`, `>`, and `&` when they appear as literal text in your content, since leaving them unescaped can break your markup or accidentally create invalid tags.

[Previous](./[9]-Comments-and-Whitespace.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[11]-Why-Semantics-Matter.md)
