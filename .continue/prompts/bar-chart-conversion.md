## Task: Change Spend Analytics Chart from Line to Bar (Balok)

**Context:** File `DASHBOARD/customer/purchases.html` has a modal popup (`#spendModal`) that opens when the "Total Pengeluaran" card is clicked. The modal contains a Chart.js graph that currently renders a **line chart**. I need to convert it to a **bar chart** (balok).

---

### What to change

In the `<script>` block (inside the same file), locate the `spendChart = new Chart(ctx, { ... })` initialization. Change:

```js
type: 'line',
```
to:
```js
type: 'bar',
```

Then update the dataset config:
- Remove `fill: true` and `backgroundColor: gradient` (bar charts don't use gradient fills the same way)
- Replace with `backgroundColor` as an array or single color (e.g., use the primary color with alpha like `'rgba(249,115,22,0.7)'`)
- Optionally add `borderColor` and `borderRadius` for rounded bars
- Keep `borderWidth: 0` or small value for bar charts

### Chart options that should stay unchanged
- All `interaction`, `tooltip`, `scales` config remain the same
- `responsive: true`, `maintainAspectRatio: false` stays
- `animation: { duration: 400 }` stays
- Dark mode color detection via `getChartColors()` stays

### Files to modify
- `DASHBOARD/customer/purchases.html` — only the Chart.js config inside the modal `<script>`

### Expected result
- Modal still opens/closes normally
- Graph renders as rounded bar chart instead of line
- Hover tooltip still works identically
- Dark/light theme still adapts colors
- ResizeObserver + theme toggle redraw still works

---

### Current Chart.js init block (for reference):

```javascript
spendChart = new Chart(chartCanvas, {
  type: 'line',
  data: {
    labels,
    datasets: [{
      label: 'Pengeluaran',
      data: values,
      borderColor: colors.primary,
      backgroundColor: gradient,
      borderWidth: 3,
      fill: true,
      tension: 0.4,
      pointBackgroundColor: colors.primary,
      pointBorderColor: colors.bg,
      pointBorderWidth: 2,
      pointRadius: granularity === 'year' ? 5 : 3,
      pointHoverRadius: 8,
      pointStyle: granularity === 'year' ? 'circle' : 'circle',
    }]
  },
  options: {
    responsive: true,
    maintainAspectRatio: false,
    animation: { duration: 400 },
    interaction: { intersect: false, mode: 'index' },
    plugins: {
      legend: { display: false },
      tooltip: {
        backgroundColor: colors.bg,
        titleColor: colors.text,
        bodyColor: colors.text,
        borderColor: colors.primary,
        borderWidth: 1,
        padding: 12,
        displayColors: false,
        bodyFont: { size: 12 },
        titleFont: { size: 12 },
        callbacks: { label: ctx => formatCurrency(ctx.parsed.y) }
      }
    },
    scales: {
      x: { grid: { display: false }, ticks: { color: colors.text, font: { size: 11 }, maxRotation: 45 } },
      y: { grid: { color: colors.grid }, ticks: { color: colors.text, font: { size: 11 }, callback: v => 'Rp ' + (v / 1000000).toFixed(0) + 'jt', maxTicksLimit: 8 } }
    }
  }
});
```

### Additional notes
- Keep the gradient removal — bar charts look better with solid colors
- Use `colors.primary` with alpha for bar fill, and maybe a slightly darker solid color for `borderColor`
- Add `borderRadius: 6` or similar for modern rounded bars
- Remove `tension`, `pointRadius`, `pointBackgroundColor`, `pointBorderColor`, `pointBorderWidth`, `pointRadius`, `pointHoverRadius`, `pointStyle` — these are line-only properties
- Remove `fill: true` and the gradient creation code — no longer needed
