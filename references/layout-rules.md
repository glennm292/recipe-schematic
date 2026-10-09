# Layout rules

## The grid model

Think of the table as a directed graph flattened onto a grid. Rows are ingredients; columns are "stages" of processing; a cell is an operation node. An operation at column c spanning rows r1..r2 means "take everything that is in rows r1..r2 at column c-1 and do this to it". Because the cell is a rectangle, the ingredients feeding any one operation must be contiguous rows. Row order is therefore the main design decision: order ingredients so that every operation's inputs are adjacent.

Procedure:

1. List operations in execution order.
2. For each operation, list its inputs: raw ingredients and/or the output of earlier operations.
3. Assign rows: start with the first operation's raw inputs, then append the raw inputs of each later operation that joins the existing chain. When an operation's inputs are two independent chains (a sauce and a filling), those chains become adjacent row groups.
4. Assign columns: an operation's column is one more than the maximum column of its inputs. Raw ingredients are at column 0 (the ingredient column itself).
5. Fill gaps: any row at any column that is not covered by an operation cell gets an empty cell. Merge horizontally adjacent empties in the same row with `colspan`; merge vertically adjacent identical empties with `rowspan` only if that does not break another cell's rectangle.
6. Verify: count cells per row including spans. Every row must equal total columns. A mismatch always means a rowspan was miscounted; recount the ingredient rows under the cell.

## Column budget

Keep to at most seven columns including ingredients and the final serve column. If an operation chain is longer, fold consecutive non-ingredient steps into one cell with a semicolon: "flip; cook 2 min more". Fewer, wider cells read better than a long thin chain.

## Cell text

- Verb first, parameter second: "whisk until smooth", "bake 350°F (175°C), 30–40 min", "fold in".
- Times and temperatures always go in the cell, because the cell is where the cook looks while timing.
- Vessel goes in the cell only when it changes ("transfer to baking sheet"); otherwise put the vessel in the component label row.
- Avoid pronouns and articles. "Add tomatoes" is wrong because the table already shows tomatoes entering; write what happens to the mixture: "simmer 20 min".

## Row conventions

- Ingredient cell: quantity, unit, (metric), name, prep. Example: `1½ oz (45 g; ¾ cup) Parmesan, grated`.
- Prep that happens before any step and only to that ingredient (minced, shredded, halved and pounded) stays in the ingredient cell, not in an operation cell.
- Component label rows span the full width minus any cells that already span across them (typically the final serve column). Style them muted so they do not compete with operation cells.
- Top rows for oven and pan preparation span the full width.

## Worked example: Best chicken Parmesan (America's Test Kitchen)

Source structure: a sauce (nine ingredients, three operations) and a chicken track (thirteen ingredients, six operations) that meet only at plating.

Columns: 0 ingredients, 1 first operation, 2, 3, 4, 5, 6 serve. Seven columns.

Sauce rows 1–9:

| row | ingredient | col 1 | col 2 | col 3 | col 4 | col 5 |
|---|---|---|---|---|---|---|
| 1 | 1 Tbs olive oil | heat until shimmering | cook 30 s (rows 1–5) | simmer 20 min (rows 1–7) | off heat, stir in; keep warm (rows 1–9) | empty (rows 1–9) |
| 2–5 | garlic, salt, oregano, pepper flakes | empty | ↑ | ↑ | ↑ | ↑ |
| 6–7 | crushed tomatoes, sugar | empty (colspan 2) | | ↑ | ↑ | ↑ |
| 8–9 | basil, 1 Tbs olive oil | empty (colspan 3) | | | ↑ | ↑ |

Chicken rows 10–22:

| row | ingredient | col 1 | col 2 | col 3 | col 4 | col 5 |
|---|---|---|---|---|---|---|
| 10–11 | chicken, 1 tsp salt | salt; rest 20 min (rows 10–11) | dredge (rows 10–18) | fry 1½–2 min per side (rows 10–19) | broil 2–4 min (rows 10–21) | 2 Tbs sauce each; pass rest (rows 10–22) |
| 12–13 | egg, flour | whisk until smooth (rows 12–13) | ↑ | ↑ | ↑ | ↑ |
| 14–18 | Parmesan, panko, garlic powder, oregano, pepper | combine (rows 14–18) | ↑ | ↑ | ↑ | ↑ |
| 19 | vegetable oil | empty (colspan 2) | | ↑ | ↑ | ↑ |
| 20–21 | mozzarella, fontina | combine (rows 20–21) | empty (rowspan 2, colspan 2) | | ↑ | ↑ |
| 22 | torn basil | empty (colspan 4) | | | | ↑ |

Column 6, "plate and serve immediately", spans rows 1–22 plus the chicken component label row. A full-width row "adjust oven rack 4 in from broiler element; heat broiler" sits above everything, and a "Sauce (medium saucepan)" label row sits above row 1.

Row-count check: row 10 has 1 + 1 + 1 + 1 + 1 + 1 cells plus the col-6 cell opened at row 1, so 7. Row 19 has 1 + colspan 2 + three inherited spans + inherited col 6, so 7. Every row balances.

The rendered HTML for this example is in `template.html`; copy its structure and replace the content.
