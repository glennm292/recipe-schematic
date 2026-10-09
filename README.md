# recipe-schematic

A skill that turns any recipe -- a URL, pasted text, a photo of a cookbook page -- into a
Cooking-for-Engineers-style schematic table: ingredients run down the left column, and each cooking
step is a merged cell to the right spanning exactly the ingredients it consumes, ending in the
finished dish.

It assumes nothing about the assistant or app it runs in: any agent that can read these files,
read or fetch a recipe, and produce HTML can use it. Start with `SKILL.md`. `references/extraction.md` covers splitting recipes into ingredients and
operations, `references/layout-rules.md` the grid rules with a worked example, and
`references/template.html` the self-contained, printable table template.
