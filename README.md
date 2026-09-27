# Syllabex Parser

A fidelity-first starter parser for syllabus PDFs. Version 0.1 performs the first milestone:

- validates and fingerprints a PDF;
- classifies each page as native, scanned, or hybrid;
- extracts character-level text, font metadata, colours, and coordinates;
- groups matching characters into rich text runs without flattening mixed formatting;
- extracts embedded images;
- renders page previews;
- emits `document.json`, `diagnostics.json`, assets, previews, and a reconstruction HTML file.

## Install

```bash
python -m venv .venv
source .venv/bin/activate
pip install -e ".[dev]"
```

## Run

```bash
syllabex-parse path/to/syllabus.pdf --output parsed-output
```

or:

```bash
python -m syllabex_parser.cli path/to/syllabus.pdf --output parsed-output
```

## Output

```text
parsed-output/
├── document.json
├── diagnostics.json
├── reconstruction.html
├── assets/images/
└── previews/
```

This release deliberately does not infer section/topic hierarchy yet. The raw faithful document is the source of truth. Semantic hierarchy should be layered on only after visual extraction is stable.

## List reconstruction (v0.2)

The parser now detects unordered, ordered, hierarchical-decimal, alphabetic, Roman-numeral, and dash lists. It infers nesting from indentation, attaches nearby wrapped continuation lines, and keeps rich text runs inside each list item. Raw text blocks remain untouched; semantic lists are an additional reconstructive layer.

## Syllabus admission gate (v0.3)

The parser now scores uploaded PDFs using several independent signals: page count, extractable text volume, curriculum language, structured numbering, learning-objective verbs, list structure, typographic hierarchy, and consistency across pages. A file must meet the score threshold and avoid hard failures before the application should create a subject tab. This is intentionally an admission classifier, not proof of authenticity; uncertain files should be sent to manual review rather than silently accepted.

## Run the complete application

The interface and parser are now one application. The upload gate posts the PDF to the Python backend; the backend parses and validates it; and the browser unlocks only when the backend returns `accepted: true`.

```bash
pip install -e .
syllabex-web
```

Open `http://127.0.0.1:8000`.

For Render, connect the repository and use the included `render.yaml`.
