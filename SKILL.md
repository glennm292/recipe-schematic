---
name: recipe-schematic
description: Convert any recipe (a URL, pasted text, a photo of a cookbook page, or a recipe already in the conversation) into a Cooking-for-Engineers-style schematic table, where ingredients run down the left column and each cooking step is a merged cell to the right that spans exactly the ingredients it consumes. Use this whenever the user asks for a recipe "schematic", "layout", "table", "diagram", "flow", "engineer-style", "grid", "chart", or "like Cooking for Engineers", and also when they share a recipe and ask to "lay it out", "visualize it", or "show the structure". Trigger even if they only give a link and say "make this into one of those tables".
---

# Recipe schematic

Turn a prose recipe into a single table that reads left to right: every ingredient is a row, every operation is a merged cell, and the right-most cell is the finished dish. The format is the one popularized by cookingforengineers.com. Its value is that a cook can see at a glance which ingredients go together, in what order, and what happens to each group, without re-reading paragraphs mid-cook.

## Workflow

1. **Get the recipe text.**
   - URL: call `web_fetch`. If the fetch fails because the URL is too long (tracking parameters), strip everything from `?` onward and retry, or `web_search` the recipe title plus site name and fetch the clean result. Recipe sites are often paywalled or truncated; an aggregator (umami.recipes, copymethat, etc.) that appears in search results frequently has the full ingredient list and numbered steps.
   - Pasted text or an image: read it directly. For an image of a printed page, transcribe quantities carefully; fractions and units are the usual OCR casualties.
   - Already in the conversation: reuse it, do not refetch.
2. **Extract two lists** before drawing anything: ingredients (with quantity, unit, and prep state) and operations (verb, what it applies to, time, temperature, vessel). Read `references/extraction.md` for how to split compound steps and handle divided ingredients.
3. **Lay out the grid** on paper first (in your reasoning): decide row order, then for each operation decide which rows it spans and which column it occupies. Check the arithmetic: every table row must sum to the same number of columns once rowspans and colspans are counted. Read `references/layout-rules.md` for the rules and a fully worked example.
4. **Render.** Default is an inline HTML table through the Visualizer (`show_widget`), because the user will usually look at it once in chat. Use the template in `references/template.html`. If the user asks for a file, something printable, or something to keep, write the same HTML to `/mnt/user-data/outputs/<recipe-slug>.html` and present it; the template is self-contained and prints cleanly.
5. **Follow with two or three sentences** of what the table cannot show: parallelism (which long waits overlap), serving size, equipment, and anything the source said that did not fit a cell. Do not restate the table.

## Core conventions (summary; details in references/layout-rules.md)

- One ingredient per row, listed in order of first use, grouped by component (sauce, filling, dough). A thin full-width label row may separate components.
- Each operation is one cell. It spans vertically exactly the ingredient rows that enter it (directly, or via an earlier cell that feeds into it). Cell text is the verb plus the critical parameter: "simmer 20 min", "fry 1½–2 min per side", "rest 20 min". Nothing longer than about eight words.
- Operations move strictly rightward. A cell's left edge touches the cells that feed it. Rows that pass through a column untouched get an empty cell, merged with neighbors via colspan where possible so the table does not look like a grid of blanks.
- Equipment prep that precedes everything (preheat oven, grease pan, set oven rack) goes in full-width rows at the top, in the order they should be started.
- The last column is a single cell spanning every ingredient row: the serving instruction.
- Quantities keep the source's units and add metric in parentheses when the source is imperial (or vice versa). Round sensibly; 1 cup flour is 120–125 g, not 120.3 g.
- Sentence case throughout, no bold inside cells, no icons.

## What to do when the recipe is awkward

- **An ingredient used in two places** (oil at the start and the end): two rows, one at each point of use, each with its split quantity.
- **Two components that merge late** (sauce and chicken; cake and frosting): stack them as row groups; they share only the final column. Do not try to draw a column that joins them earlier.
- **A step with no new ingredient** (flip, drain, rest, transfer to pan): it is still a cell; it spans the same rows as the cell to its left.
- **Very long recipes** (30+ ingredients): still one table, but confirm with the user whether they want the components split into separate tables for readability.
- **Optional or "to taste" items**: a row like any other, with "to taste" as the quantity.
