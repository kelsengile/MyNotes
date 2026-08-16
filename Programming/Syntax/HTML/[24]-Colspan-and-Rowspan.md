[Previous](./[23]-Table-Sections-and-Accessibility.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[25]-Form-Basics.md)

*Tables*

# Lesson 24 - Merging Cells: `colspan` & `rowspan`

## 24.1 `colspan`

`colspan` makes a cell span multiple **columns**, useful for section headers that cover several columns below them.

```html
<table>
  <tr>
    <th colspan="2">Contact Info</th>
  </tr>
  <tr>
    <td>Email</td>
    <td>Phone</td>
  </tr>
</table>
```

---

## 24.2 `rowspan`

`rowspan` makes a cell span multiple **rows**, useful when one label applies to several rows beneath it.

```html
<table>
  <tr>
    <th rowspan="2">Fruit</th>
    <td>Apple</td>
  </tr>
  <tr>
    <td>Banana</td>
  </tr>
</table>
```

---

## 24.3 Combining Both

`colspan` and `rowspan` can be used together in the same table to build more complex grids, such as a schedule with merged time slots.

```html
<table>
  <tr>
    <th></th>
    <th>Monday</th>
    <th>Tuesday</th>
  </tr>
  <tr>
    <th>Morning</th>
    <td colspan="2">Team Meeting</td>
  </tr>
  <tr>
    <th rowspan="2">Afternoon</th>
    <td>Design Review</td>
    <td rowspan="2">Focus Time</td>
  </tr>
  <tr>
    <td>1:1s</td>
  </tr>
</table>
```

When cells are merged, remember to leave out the corresponding `<td>`/`<th>` that would otherwise have occupied that spot — the browser calculates the grid based on the spans, and an extra cell will shift the rest of the row.

[Previous](./[23]-Table-Sections-and-Accessibility.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[25]-Form-Basics.md)
