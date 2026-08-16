[Previous](./[31]-Responsive-Grids.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[33]-Responsive-Design-Principles.md)

*CSS Grid*

# Lesson 32 - Grid Vs. Flexbox: When To Use Which

## 32.1 The Core Difference

- **Flexbox** is **one-dimensional** — it lays items out along a single axis (a row *or* a column) at a time (Lesson 24).
- **Grid** is **two-dimensional** — it lays items out along rows *and* columns simultaneously (Lesson 28).

This single distinction drives almost every decision about which to reach for.

```css
/* Flexbox: one dimension, content-driven */
.flex-container {
  display: flex;
  gap: 12px;
}

/* Grid: two dimensions, layout-driven */
.grid-container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: auto auto;
}
```

---

## 32.2 When Content Size Should Drive Layout: Flexbox

Flexbox is ideal when you want the **content itself** to determine how much space each item takes, and items to flow naturally based on that content — like a row of buttons, tags, or navigation links.

```css
.tag-list {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
}
```

---

## 32.3 When Layout Should Drive Content Placement: Grid

Grid is ideal when you want to define an overall structure **first**, and then place content into it — like a full page layout with header, sidebar, main content, and footer.

```css
.page {
  display: grid;
  grid-template-areas:
    "header header"
    "sidebar main"
    "footer footer";
  grid-template-columns: 200px 1fr;
}
```

---

## 32.4 Quick Decision Guide

| Scenario | Better Tool |
|---|---|
| Navigation bar, button group, toolbar | Flexbox |
| Full page layout (header/sidebar/footer) | Grid |
| Centering a single element | Flexbox |
| Card grid with consistent rows and columns | Grid |
| A row of items that should wrap naturally | Flexbox |
| Precise alignment across both rows and columns | Grid |
| Component whose size should follow its content | Flexbox |
| Layout that should stay consistent regardless of content | Grid |

---

## 32.5 Using Them Together

Grid and Flexbox aren't mutually exclusive — real projects commonly use Grid for overall page structure, and Flexbox for the internal layout of individual components within that structure.

```css
.page {
  display: grid;
  grid-template-columns: 200px 1fr;
}

.navbar {
  display: flex; /* Flexbox handles alignment *within* this one component */
  justify-content: space-between;
  align-items: center;
}
```

This combination — Grid for the macro layout, Flexbox for the micro layout — is the most common pattern in modern CSS architecture.

[Previous](./[31]-Responsive-Grids.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[33]-Responsive-Design-Principles.md)
