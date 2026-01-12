---
description: Open file paths mentioned in Claude's last response
---

Look at your most recent response (the last Claude turn only) and find any file paths you mentioned.

- If no files were mentioned: tell the user "No files mentioned in my last response."
- If exactly one file was mentioned: open it immediately using the macOS `open` command.
- If multiple files were mentioned: list them numbered (1, 2, 3...) and ask the user which ones to open. The user will type numbers (e.g., "1" or "1 3") and press enter. Then open the selected files.
