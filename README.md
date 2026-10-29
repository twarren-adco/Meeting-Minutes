# Meeting Minutes

## Project Start-Up Checklist (`checklist/index.html`)

Live, Excel-style start-up checklist for the Weinam Ck Car Park ECI & Early Works, published as a private Claude artifact:
https://claude.ai/artifact/4NontSLKALcwM1iqVr6VQb

- Task data lives in the artifact's shared database (`tasks` collection, `project/meta` doc), not in this file, so republishing the page never overwrites the team's edits.
- Editing: click a cell, then type, or press Enter/F2. Tab and the arrow keys move between cells, Delete clears a cell, Ctrl+V pastes a block copied from Excel, Ctrl+Z undoes. Use the row number for insert, move and delete.
- Click the star next to a task (or use the row menu) to make it a key task. Key tasks are listed with the key dates, in date order, and can be filtered with the "Key tasks" chip.
- **Timeline** tab: a Gantt chart of every task from Start By to Complete By, coloured by status, with Today and key-task lines. Drag a bar to reschedule, drag its ends to change start or finish; Weeks/Months zoom.
- **Contacts** tab: project details plus the master contact list, grouped by team and edited in place. Contact data lives in the artifact database (`contacts` collection), not in this repo.
- **Export to Excel** fills a cleaned copy of the team's "Project Start-Up Checklist - ECI" workbook (embedded in the page as `#xlsxTemplate`), so the download keeps its formatting, status colours, dropdowns and Dashboard formulas, plus ID, Key Task and Completed On columns and a Contact List sheet.
- Colours follow adcoconstruct.com.au: ADCO blue `#0047BB` on white, with its pale blue tints. ADCO's own fonts (Filson Pro, Grotzec Cond) are licensed, so the page uses Figtree and Barlow Condensed as stand-ins.

## Personal dashboard (`personal/index.html`)

Tom's private dashboard in the Weinam page's ADCO styling. The **Dashboard** tab (landing page) summarises this week's minutes like the Weinam dashboard: progress, goals, status by day, open actions, misc list by heading and recent weeks. **My minutes** is the weekly to-do board rebuilt from `weekly_todo_board.html`, **Misc list** is its standing list, and **Weinam Ck Car Park** opens the Weinam checklist. Published as a separate private Claude artifact:
https://claude.ai/artifact/TJRF8a1YwAwFGHv5cVFxVz

- Each viewer's weeks and misc list are stored in their own private area of the page's database (`data/users/<id>/week-YYYY-MM-DD` and `.../misc`), so nothing is visible to anyone else even if the page is shared.
- The Dashboard also shows **Weinam Ck Car Park – my actions (TW)**: a read-only copy of the Weinam tasks where Champion is TW, written to `data/users/<id>/weinam-tw` by a scheduled Claude task ("Weinam TW actions → personal dashboard", weekdays hourly 6:07am–6:07pm Brisbane time). The two pages can't read each other's databases, so this copy is how the personal page sees Weinam.
- **Back up / Restore** use the same file format as the old board, so a backup from the old board can be restored here.
