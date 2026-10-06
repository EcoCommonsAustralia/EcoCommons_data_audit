# EcoCommons Data Audit — Blog

🔗 **Live site: <https://ecocommonsaustralia.github.io/EcoCommons_data_audit/>**

A [Quarto](https://quarto.org) website publishing data-audit write-ups for the
**EcoCommons Australia** and **Biosecurity Commons** platforms. Styled to match the
[EcoCommons notebook blog](https://ecocommonsaustralia.github.io/notebook-blog).

The site output is generated into `docs/` for GitHub Pages hosting.

## Posts

| Post | Source |
|------|--------|
| [Auditing the EcoCommons Production Data Catalogue](https://ecocommonsaustralia.github.io/EcoCommons_data_audit/ecocommons_prod_data_audit.html) | `ecocommons_prod_data_audit.qmd` |
| [The EcoCommons Production Data Catalogue](https://ecocommonsaustralia.github.io/EcoCommons_data_audit/prod_dataset_catalogue.html) | `prod_dataset_catalogue.qmd` |
| [Rule Book](https://ecocommonsaustralia.github.io/EcoCommons_data_audit/rule_book.html) | `rule_book.qmd` |

## Building

```bash
quarto render          # build the whole site into docs/
quarto preview         # live-reload preview while editing
```

## Adding a post

1. Create `<name>.qmd` at the repo root (follow the front-matter + banner style of the existing post).
2. Add it to the navbar in `_quarto.yml` and link it from `index.qmd`.
3. `quarto render` and commit, including the updated `docs/`.

## Notes

- `styles/ec_html_template.css` is the shared EcoCommons stylesheet.
- No credentials belong in this repo — posts reference secrets by variable name only.
- Underlying audit data and extraction tooling live in the separate `EcoCommons_data_audit` repo (`prod_dataset_catalogue/`).
