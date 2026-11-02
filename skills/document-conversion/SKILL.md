---
name: document-conversion
description: |
  Convert PDF, Word, Excel and PowerPoint files, pull tables out of PDFs, and turn bank statement PDFs into transactions, via x402 or MPP.

  USE FOR:
  - Reading a PDF, DOCX, XLSX, PPTX, HTML or EPUB file as Markdown
  - Extracting the tables of a PDF as JSON, CSV or Excel
  - Turning a bank statement PDF into dated transactions (CSV, Excel, OFX, QIF)
  - Converting Word, Excel or PowerPoint files to PDF
  - Converting a PDF to an editable Word file

  TRIGGERS:
  - "pdf to markdown", "read this pdf", "document to text", "docx to markdown"
  - "extract tables", "pdf table to csv", "pdf to excel"
  - "bank statement to csv", "parse bank statement", "statement transactions"
  - "convert to pdf", "docx to pdf", "pdf to word"

  Use `npx agentcash@latest fetch` for docs.tanod.dev endpoints.

  IMPORTANT: Use exact endpoint paths from the Quick Reference table below.
metadata:
  version: 1
---

# Document Conversion with Tanod Documents

Five document jobs at `https://docs.tanod.dev`. Every route takes a public `url` (at most 20 MB) or the file as base64 (at most 10 MB), and is charged only when it returns HTTP 200.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Quick Reference

| Task | Endpoint | Price |
|------|----------|-------|
| Document to Markdown | `POST https://docs.tanod.dev/v1/docs/to-markdown` | $0.005 |
| PDF tables to JSON/CSV/XLSX | `POST https://docs.tanod.dev/v1/pdf/tables` | $0.01 |
| Bank statement to transactions | `POST https://docs.tanod.dev/v1/pdf/bank-statement` | $0.01 |
| Office file to PDF | `POST https://docs.tanod.dev/v1/docs/to-pdf` | $0.01 |
| PDF to Word | `POST https://docs.tanod.dev/v1/pdf/to-docx` | $0.01 |

Prices are per successful call, paid in USDC on Base or Solana (x402) or on Tempo (MPP).

## Document to Markdown

Reads PDF, DOCX, XLSX, PPTX, HTML or EPUB and keeps headings, lists, links, tables and slide notes (images are not extracted).

```bash
npx agentcash@latest fetch https://docs.tanod.dev/v1/docs/to-markdown -m POST -b '{"url": "https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf"}'
```

**Options:**
- `url` or `file_base64` (one is required)
- `filename` - optional name; the format is detected from the content
- `reading_order` - `auto` (default) or `columns` for 2-4 column PDF pages

**Returns:** `markdown` (at most 500,000 characters; `truncated` says if it was cut), `format`, `characters`, and `pages`, `sheets` or `slides` where known.

## PDF Tables

Every table on the selected pages, ruled or aligned text.

```bash
npx agentcash@latest fetch https://docs.tanod.dev/v1/pdf/tables -m POST -b '{"url": "https://tanod.dev/samples/tables-sample.pdf", "format": "csv"}'
```

**Options:**
- `url` or `file_base64` (one is required)
- `pages` - e.g. `1-5,9` (default all, at most 200 per call)
- `format` - `json` (default), `csv` (one file per table) or `xlsx` (one sheet per table)
- `output` - `base64` (default) or `url` (a link kept for 24 hours)

**Returns:** each table with its `page`, `header` and `rows`; files for `csv` and `xlsx`.

## Bank Statement to Transactions

Reads a bank statement PDF into dated, signed transactions and checks every row against the running balance.

```bash
npx agentcash@latest fetch https://docs.tanod.dev/v1/pdf/bank-statement -m POST -b '{"url": "https://tanod.dev/samples/statement-sample.pdf", "format": "csv"}'
```

**Options:**
- `url` or `file_base64` (one is required)
- `pages` - default all
- `format` - `json` (default), `csv`, `xlsx`, `ofx` or `qif`
- `output` - `base64` (default) or `url`

**Returns:** transactions (`date`, `description`, `amount`, `balance`, `row_ok`), opening and closing balance, `flagged_rows` for rows that fail the balance check, and a `confidence` from 0 to 1.

## Office File to PDF

DOCX, XLSX, PPTX, ODT, ODS, ODP, RTF or plain text to PDF. Legacy .doc/.xls/.ppt are not accepted.

```bash
npx agentcash@latest fetch https://docs.tanod.dev/v1/docs/to-pdf -m POST -b '{"url": "https://calibre-ebook.com/downloads/demos/demo.docx"}'
```

**Options:**
- `url` or `file_base64` (one is required)
- `output` - `base64` (default) or `url`

**Returns:** the PDF in `file` (base64) with `pages`.

## PDF to Word

A .docx that looks like the PDF; each line is a positioned text box, so it is not reflowable text. At most 50 pages per call.

```bash
npx agentcash@latest fetch https://docs.tanod.dev/v1/pdf/to-docx -m POST -b '{"url": "https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf"}'
```

**Options:**
- `url` or `pdf_base64` (one is required)
- `pages` - convert a long PDF in parts
- `output` - `base64` (default) or `url`

**Returns:** the Word file in `file` (base64), `converted_pages` and `warnings`.

## When to Use What

| Scenario | Use |
|----------|-----|
| Read a document's text | to-markdown ($0.005) |
| Need the numbers in a PDF's tables | pdf/tables ($0.01) |
| Bank statement for bookkeeping or import | pdf/bank-statement ($0.01): it reconciles rows; check `flagged_rows` |
| Need a PDF from an Office file | docs/to-pdf ($0.01) |
| Need to edit a PDF in Word | pdf/to-docx ($0.01); layout is kept, text is not reflowed |

## Notes

- Private, internal and IP-literal URLs are refused; pass the file as base64 instead.
- Scanned PDFs have no text layer: to-markdown returns little text for them.
- `npx agentcash@latest discover https://docs.tanod.dev` lists the other PDF routes (merge, split, OCR, compress, forms).
