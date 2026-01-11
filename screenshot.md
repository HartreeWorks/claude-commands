---
description: Take a phone screenshot and save to project assets
allowed-tools: Bash(adb:*, mkdir:*, mktemp:*, sips:*, rm:*, ls:*, du:*, printf:*)
arguments:
  - name: folder
    description: Folder name for screenshots
    required: false
---

Take a screenshot from the connected Android phone.

If `$ARGUMENTS.folder` is provided, use that as the folder name. Otherwise, ask the user what folder name to use.

Then run this script with the folder name:

```bash
FOLDER_NAME="<folder-name>"
BASE_DIR="assets/screenshots"
TARGET_DIR="$BASE_DIR/$FOLDER_NAME"

mkdir -p "$TARGET_DIR"

# Find next number
NEXT=$(printf "%02d" $(($(ls -1 "$TARGET_DIR"/*.jpg 2>/dev/null | wc -l) + 1)))

# Take screenshot as PNG first, then convert to compressed JPEG
TEMP_PNG=$(mktemp /tmp/screenshot.XXXXXX.png)
OUTPUT="$TARGET_DIR/$NEXT.jpg"

adb exec-out screencap -p > "$TEMP_PNG"

if [ -s "$TEMP_PNG" ]; then
    # Convert to JPEG at 60% quality using sips (built into macOS)
    sips -s format jpeg -s formatOptions 60 "$TEMP_PNG" --out "$OUTPUT" >/dev/null 2>&1
    rm "$TEMP_PNG"

    SIZE=$(du -h "$OUTPUT" | cut -f1)
    echo "Saved: $OUTPUT ($SIZE)"
else
    echo "Error: Screenshot failed" >&2
    rm -f "$TEMP_PNG"
    exit 1
fi
```

Report the saved file path and size to the user.
