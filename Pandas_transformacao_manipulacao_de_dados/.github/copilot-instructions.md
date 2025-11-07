## Repo snapshot

- Primary language: Python (Jupyter notebooks). Main files are notebooks under the repo root: `notebook.ipynb`, `exercise_notebook copy.ipynb`, `exercise2_notebook.ipynb`.
- Dataset files (JSON) live in the repo root and are read with relative paths: `dados_hospedagem.json`, `ex_dados_locacao_imoveis.json`, `ex_dados_vendas_clientes.json`, `moveis_disponiveis.json`.

## Big picture / architecture

- This repository is a small collection of instructional notebooks that demonstrate Pandas data transformation and cleaning. There is no multi-module application or service boundary—workflows are notebook-driven.
- Data flow: notebooks read JSON files (pd.read_json), normalize/explode nested fields (pd.json_normalize + explode), cast types, clean currency strings, and tokenize/clean text columns. Outputs are typically inspected in-cell using `.head()` / `.info()`.

## Developer workflow (how humans work here)

- Open a notebook in VS Code (or Jupyter) and run cells sequentially. Cells assume the current working directory is the repository root; data paths are relative.
- Typical setup (discoverable from notebooks): Python + pandas + numpy. There is no `requirements.txt`; create a virtual environment and install at minimum:

  pip install pandas numpy

- Common quick actions you may need to suggest or automate:
  - Convert price strings: remove `$` and `,`, then astype float (see `notebook.ipynb` example).
  - Explode nested arrays after json_normalize: call `.explode()` and `reset_index(drop=True)`.

## Project-specific code patterns and conventions

- Notebooks use pandas heavy in-place transformation patterns: assign columns directly, e.g. `dados['preco'] = dados['preco'].apply(lambda x: ...)` then `dados['preco'] = dados['preco'].astype(np.float64)`.
- When cleaning multiple columns with the same operation, notebooks use `applymap` on DataFrame slices, e.g. `dados[['taxa_deposito','taxa_limpeza']] = dados[['taxa_deposito','taxa_limpeza']].applymap(lambda x: ...)`.
- Text cleaning examples: lowercasing (`.str.lower()`), regex replace to remove non-alphanumerics: `.str.replace('[^a-zA-Z0-9\\-\\']',' ', regex=True)` and special hyphen handling `(?<!\\w)-(?!\\w)`.

## Key files to inspect when making changes

- `notebook.ipynb` — canonical walkthrough: data reading, normalization, dtype casting, price cleaning, tokenization.
- `exercise_notebook copy.ipynb` and `exercise2_notebook.ipynb` — additional examples/exercises following the same patterns.
- JSON files in repo root — these are the canonical inputs for the notebooks. Any I/O changes must keep the paths relative to repo root.

## Examples (copyable guidance for an AI agent)

- Clean currency column named `preco` (pattern used in `notebook.ipynb`):

  dados['preco'] = dados['preco'].apply(lambda x: x.replace('$','').replace(',','').strip())
  dados['preco'] = dados['preco'].astype(np.float64)

- Explode nested furniture info after normalization:

  dados = pd.json_normalize(dados_hospedagem['info_moveis'])
  dados = dados.explode(list(dados.columns)[3:])
  dados.reset_index(drop=True, inplace=True)

## Integration points and external dependencies

- No external services, APIs or CI found in the repository. Notebooks assume local JSON files only.
- Required runtime libraries are discoverable from imports in notebooks: `pandas`, `numpy`. If you add scripts or tests, include a `requirements.txt` or `pyproject.toml`.

## What not to change without asking

- Don't move or rename JSON dataset files without updating notebooks: all I/O is relative and notebooks do not currently handle missing paths.
- Avoid converting notebooks into scripts unless tests or CLI wrappers are added—notebooks are the primary authoring and teaching format here.

## Suggested prompts / tasks for an AI coding agent

- "Create a `requirements.txt` that pins pandas and numpy to a recent stable version and add a short README describing how to run the notebooks." — low risk, high value.
- "Refactor the price cleaning into a small helper function and reuse it across notebooks; add a short unit test file that validates price normalization for a few cases." — moderate risk; preserves notebooks but adds test harness.

## Missing or unclear items you can ask the user about

- Preferred Python version and whether they want a `requirements.txt` or a conda environment file.
- Whether notebooks should be converted to runnable scripts for CI or kept purely as learning artifacts.

If anything above is unclear or you want the file reorganized (shorter/longer or more/less prescriptive), tell me which sections to adjust and I will iterate.
