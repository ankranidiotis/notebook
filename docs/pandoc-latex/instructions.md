# How to Create Essays with Pandoc and Markdown

## Introduction
Writing essays in Markdown and converting them to LaTeX, Microsoft Word, or PDF using **Pandoc** is a straightforward process. This workflow allows you to maintain a clean, text-based writing environment while producing professionally formatted documents.

## Prerequisites
Before you begin, ensure that you have the following tools installed:

- **Pandoc** — install it with:  
  `sudo apt install pandoc`
- **LaTeX** — install it with:  
  `sudo apt install texlive-full`

## Document and Citation Style
To format your document according to the publisher’s requirements, you may need:

- A **LaTeX template** provided by the editor (e.g., `eisvogel.latex`)
- A **citation style** file required by the editor (e.g., `ieee.csl`)

You can find citation styles [here](https://www.zotero.org/styles).  
Right-click on the desired style and select **“Save Link As…”** to download it.

## File Structure
Your project directory should have the following structure:

```
project/
├─ paper.md
├─ bibliography.bib
├─ editor_style.latex
└─ citations_style.csl
```

## YAML Header
At the beginning of your Markdown file, include a YAML metadata block like this:

```yaml
---
title: "Test Title"
author: "your name"
date: "2025-11-01"
abstract: "This paper is testing Pandoc technology..."
keywords: [Markdown, Pandoc, LaTeX, something]
bibliography: bibliography.bib

csl: ieee.csl

titlepage: true
titlepage-text-color: "000000"
toc: true

mainfont: Times New Roman
---
```

# Generating the Document
To convert your Markdown file into a PDF, run the following command:
```
pandoc essay.md \
  --from markdown --to pdf \
  --output final_paper.pdf \
  --citeproc \
  --template=eisvogel.latex \
  --pdf-engine=xelatex \
  -V mainfont="Times New Roman" \
  -V sansfont="Times New Roman" \
  -V lang=el \
  --number-sections
  ```

# Writing in Markdown
### Numbered Equations and References
Here is an example of a numbered equation:

```
\begin{equation}
T[i,s] \equiv T_{r,c} = v
\label{eq:basic_representation}
\end{equation}
```

TTo reference this equation within your text, write `\ref{eq:basic_representation}`.

### Images
Include images using the following syntax:

```
![Elementary (blue) and derived (red) operators.](primary_operators.png){width=70%}{#fig:myfigure}
```

The text inside the square brackets serves as the caption.
Image numbering is handled automatically.
You can refer to this image later in the text using `\ref{fig:myfigure}`.

### Tables
To add a caption below a table, use the following format:

```
Table: Caption for the table of operators {#tbl:operators}
```

You can refer to this table later in the text using `\ref{tbl:operators}`.

### Bibliography

If we need a word to start with a capital letter, we can put curly brackets around it in the bibliography file, e.g. `{M}athematics`.
