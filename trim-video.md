---
description: Trim seconds from the start and/or end of video files using ffmpeg
allowed-tools: Bash, Read, AskUserQuestion
---

# Video Trim Utility

You are helping the user trim video files. The user wants to remove seconds from the beginning and optionally from the end of videos.

## Instructions

1. First, check if a video file was specified in the arguments: $ARGUMENTS

2. **If no file was specified:**
   - Search for video files in the current working directory:
     ```bash
     find . -maxdepth 1 -type f \( -iname "*.mp4" -o -iname "*.mov" -o -iname "*.mkv" -o -iname "*.avi" -o -iname "*.webm" \) | sort
     ```
   - If no video files are found, inform the user and ask them to provide a path to a video file.
   - If video files ARE found, use AskUserQuestion with `multiSelect: true` to let the user pick which video(s) to trim. List each video file as an option (up to 4 - if more exist, show the first 4 and mention there are more).

3. For each selected video file:

   a. Verify the file exists using `ls -la` on the file path.

   b. Get the video duration using ffprobe:
      ```bash
      ffprobe -v error -show_entries format=duration -of csv=p=0 "FILE_PATH"
      ```

   c. Display the duration to the user in a human-readable format (e.g., "2 minutes 34 seconds").

4. Use AskUserQuestion to ask the user TWO questions:
   - How many seconds to trim from the START of the video (provide common options like 3, 5, 10 seconds)
   - How many seconds to trim from the END of the video (include 0 as an option for "none")

5. For each video, calculate the new start time and duration:
   - Start time = seconds to trim from start
   - New duration = original_duration - start_trim - end_trim

6. Run ffmpeg to trim each video:
   ```bash
   ffmpeg -ss START_SECONDS -i "INPUT_FILE" -t NEW_DURATION -c copy "OUTPUT_FILE"
   ```

   The output file should have the same name but with `-trimmed` before the extension.
   For example: `video.mp4` becomes `video-trimmed.mp4`

7. Verify each output file was created and show its details.

## Important Notes

- Use `-c copy` to avoid re-encoding (faster, no quality loss)
- If the user specifies trim values that exceed the video duration, warn them and ask for corrected values
- Always show the original duration before asking for trim values
- Apply the same trim values to all selected videos (ask once, apply to all)
- Supported formats: MP4, MOV, MKV, AVI, WebM
