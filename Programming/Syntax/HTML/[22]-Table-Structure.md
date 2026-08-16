[Previous](./[21]-Canvas-Element-Basics.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[23]-Table-Sections-and-Accessibility.md)

*Tables*

# Lesson 22 - Table Structure

## 22.1 `<table>`, `<tr>`, `<td>`

A table is built from `<table>`, with each row as a `<tr>` (table row), and each cell as a `<td>` (table data).

```html
<table>
  <tr>
    <td>Apple</td>
    <td>$1.50</td>
  </tr>
  <tr>
    <td>Banana</td>
    <td>$0.75</td>
  </tr>
</table>
```

---

## 22.2 `<th>`

`<th>` (table header) marks a cell as a header rather than data — browsers render it bold and centered by default, and it carries semantic meaning that helps screen reader users understand which column or row a cell belongs to.

```html
<table>
  <tr>
    <th>Fruit</th>
    <th>Price</th>
  </tr>
  <tr>
    <td>Apple</td>
    <td>$1.50</td>
  </tr>
</table>
```

---

## 22.3 Basic Table Example

Putting it together, a small product table:

```html
<table>
  <tr>
    <th>Product</th>
    <th>Price</th>
    <th>In Stock</th>
  </tr>
  <tr>
    <td>Apple</td>
    <td>$1.50</td>
    <td>Yes</td>
  </tr>
  <tr>
    <td>Banana</td>
    <td>$0.75</td>
    <td>No</td>
  </tr>
</table>
```

Tables should only be used for genuinely tabular data — never for page layout. In the next lesson we'll look at grouping table content and making tables more accessible.

[Previous](./[21]-Canvas-Element-Basics.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[23]-Table-Sections-and-Accessibility.md)
