# Zotero & Obsidian: Academic Citation Workflow
Goal: Quickly insert in-text citations (e.g., [1]) and automatically generate a bibliography list.

## Tools (Plugins)
1. Zotero Integration
2. Pandoc Reference List

## Zotero Integration Settings
Go to Settings → Zotero Integration → Citation Format

   - **Citation format**: custom style name
   - **Output format**: Pandoc (This is the critical step for integration)

## Pandoc Reference List Settings
Go to Settings → Pandoc Reference List

   - Pull bibliography from Zotero: ✅ (Check this box)

   - Citation style: Select a pre-defined citation style (e.g. APA)

## Usage: Insert Citation
- In your note, press Ctrl/Cmd + P.

- Run the command: Zotero Integration: [Style Name].

- Select the source. Result: The in-text citation is inserted (e.g., [1]).

## View Live Bibliography
- Press Ctrl/Cmd + P.

- Run the command: Pandoc Reference List: Show reference list.

- Result: A side pane opens, displaying the live, fully formatted bibliography for all sources cited in the current document.