[Previous](./[22]-Table-Structure.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[24]-Colspan-and-Rowspan.md)

*Tables*

# Lesson 23 - Table Sections & Accessibility

## 23.1 `<thead>`, `<tbody>`, `<tfoot>`

These elements group a table into logical sections: `<thead>` for header rows, `<tbody>` for the main data, and `<tfoot>` for summary rows like totals.

```html
<table>
  <thead>
    <tr><th>Product</th><th>Price</th></tr>
  </thead>
  <tbody>
    <tr><td>Apple</td><td>$1.50</td></tr>
    <tr><td>Banana</td><td>$0.75</td></tr>
  </tbody>
  <tfoot>
    <tr><td>Total</td><td>$2.25</td></tr>
  </tfoot>
</table>
```

Grouping also lets you style each section independently with CSS and lets browsers keep the header visible while scrolling a long table.

---

## 23.2 `<caption>`

`<caption>` provides a title for the whole table, placed immediately after the opening `<table>` tag.

```html
<table>
  <caption>Monthly Fruit Prices</caption>
  <thead>
    <tr><th>Product</th><th>Price</th></tr>
  </thead>
  ...
</table>
```

A caption is announced by screen readers before the table's content, giving context that a plain heading above the table doesn't reliably provide.

---

## 23.3 The `scope` Attribute

`scope` on a `<th>` tells assistive technology whether the header applies to a column or a row — essential for complex tables to remain understandable when read aloud cell by cell.

```html
<table>
  <tr>
    <th scope="col">Product</th>
    <th scope="col">Price</th>
  </tr>
  <tr>
    <th scope="row">Apple</th>
    <td>$1.50</td>
  </tr>
</table>
```

For simple tables, `scope="col"` on the header row is usually enough — reserve `scope="row"` for tables where the first column also acts as a header.

[Previous](./[22]-Table-Structure.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[24]-Colspan-and-Rowspan.md)
