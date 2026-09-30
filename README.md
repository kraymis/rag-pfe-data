# stages-hybrid-rag

## Project objective

This project will develop a hybrid information retrieval system for internship
("stage") information from an engineering school. The intended system will
combine structured data queried with SQL, textual information retrieved with
RAG, and a query router that selects SQL, RAG, or both as appropriate.

## Current phase: data exploration and quality analysis

The current work is limited to understanding the source Excel dataset and
assessing its structure and data quality. The original data must remain
untouched during exploration. No cleaning rules or transformations should be
applied until the observations support explicit decisions.

Place the source workbook at
`data/raw/ExportStagesentreprisessujets2020-2027.xlsx`, then run
`notebooks/01_data_exploration.ipynb` from this project. The notebook loads the
workbook as `df_raw` and uses copies only for exploratory calculations.

## Planned future phases

1. Data cleaning and preprocessing based on documented exploration findings.
2. A structured SQL layer for fields suited to filtering and aggregation.
3. A RAG layer for searching internship descriptions and other text.
4. Hybrid SQL + RAG retrieval.
5. Query routing to choose SQL, RAG, or a combination of both.

These phases are future work; this scaffold does not implement them.

## Project structure

```text
stages-hybrid-rag/
├── data/
│   ├── raw/          # Original source data; preserved as provided
│   ├── interim/      # Temporary generated datasets (not tracked)
│   └── processed/    # Generated datasets (not tracked)
├── notebooks/
│   └── 01_data_exploration.ipynb
├── src/
│   ├── data/         # Future data quality and preprocessing modules
│   ├── rag/          # Future RAG modules
│   └── sql/          # Future SQL layer
├── reports/          # Analysis reports
├── vectorstore/      # Local vector indexes (not tracked)
├── requirements.txt
└── .gitignore
```

The `src` modules are intentionally empty placeholders. No database, embeddings,
vector store, chatbot, or application logic is included in this initial phase.
