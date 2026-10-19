# Data Visualization (charts, dashboards, 3D, statistical breakdowns)

Use for: any chart, graph, plot, dashboard, 3D/isometric view, or statistical/"demographic"
breakdown produced as part of a coding task — in any medium (web artifact, inline SVG,
matplotlib/plotly/D3, a rendered image, a Slack-shared chart).

## When to use

Load this before writing the first line of chart code, choosing chart colors, or laying out a
dashboard — including when visualization is a side-output of another domain's task (e.g.
charting genomics pipeline QC metrics, or rendering a geospatial raster).

## Picking the right form

Match the form to the question being asked, not to what looks impressive:

- **Comparison across categories** → bar chart (horizontal for long labels, vertical for few
  categories with short labels).
- **Trend over time** → line chart. Don't use a bar chart for a continuous time series.
- **Distribution of a single variable** → histogram or box/violin plot, not a pie chart.
- **Part-to-whole breakdown** (what people often mean by "demographic breakdown") → a single
  stacked bar or a small set of grouped bars reads far better than a pie chart once there are
  more than ~4 categories; reserve pie charts for exactly 2-4 categories with large differences.
- **Relationship between two continuous variables** → scatter plot; add a trend line only when
  a real relationship, not noise, is being claimed.
- **Network/hierarchy** → node-link diagram (use `graph-network-analysis.md`'s layout guidance)
  or a treemap for strictly hierarchical part-to-whole data.
- **Geospatial** → a map, not a scatter plot with lat/lon as generic x/y (see
  `geospatial-analysis.md` for projection correctness).
- **3D/isometric** → reach for a true 3D view only when the third dimension carries real data
  (a surface, a volumetric field, a 3D point cloud); a 3D bar chart of inherently 2D data
  (category × value) is harder to read than its 2D equivalent and should be avoided even when
  it looks more "advanced."

## Color and accessibility

- Use a small, consistent categorical palette (6-8 hues) for discrete categories; use a
  single-hue sequential palette for ordered/continuous values, and a diverging palette only
  when there's a meaningful zero/midpoint (e.g. positive vs. negative change).
- Never rely on color alone to carry meaning — pair with position, label, or pattern for
  colorblind-safe and print-safe charts.
- Define the palette once as tokens/constants and reuse it across every chart in a given
  deliverable so a dashboard reads as one system, not a collage of mismatched defaults.

## Workflow

1. Identify the question the chart needs to answer in one sentence before picking a chart type.
2. Pick the form from the table above; resist defaulting to whatever the plotting library makes
   easiest.
3. Label axes, units, and the time/sample window explicitly — a chart with an unlabeled axis is
   not a finished chart.
4. For a dashboard with multiple panels, establish one shared visual language (palette, type
   scale, spacing) across all panels before building any single one.
5. Before calling a visualization done, check it at the size it will actually be viewed at
   (e.g. a mobile-width artifact) — dense charts that work on a 27" monitor often become
   illegible at phone width.

## Pitfalls

- Truncated/non-zero y-axes on bar charts, which exaggerate differences.
- Dual y-axes with mismatched scales implying a correlation that isn't there.
- 3D pie charts and other chart-junk that actively distorts the visual proportions being shown.
- Overplotting in scatter plots with large N — switch to a density/hexbin representation
  instead of thousands of overlapping points.

## Escalating beyond this file

This is form-selection and taste judgment, not a plotting library reference. For deep,
library-specific API surface (matplotlib/plotly/D3 specifics, publication-figure export
requirements, accessibility-contrast validators), defer to a dedicated visualization skill if
one is installed and available — see `escalation-and-scope.md`.
