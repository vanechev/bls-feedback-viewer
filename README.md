# BLS Performance Feedback Viewer

A single-file, client-side web app that lets students review their own Basic
Life Support (BLS) OSCE performance: four synced camera angles, an
AI-detected action timeline, an auto-generated transcript, and written
rubric feedback with a total score.


## Privacy by design

Everything runs locally in the browser tab. Videos are loaded via
`URL.createObjectURL` and the feedback file via `FileReader` — nothing is
ever uploaded to a server. Closing the tab discards all loaded data.

## Usage

1. Open `bls-feedback-viewer.html` in a modern browser (Chrome, Edge,
   Firefox, Safari).
2. Click **Open your 4 videos…** and select all four camera-angle `.mp4`
   files for a session at once. They're sorted alphabetically by filename
   to assign a stable angle order.
3. Click **Open your feedback file (.json)…** and select the matching
   annotation/feedback file (see schema below).
4. Use the transport controls, seekbar, or click any Events/Transcript row
   to navigate. Click a thumbnail to swap it into the main video view.
   Toggle **CC** to burn the transcript onto the video as captions.

## Expected JSON schema

```jsonc
{
  "meta": {
    "videoId": "V1-s1_part1",       // shown as the session name
    "videoFileName": "V1-s1_part1.mp4",
    "assessor": "",
    "savedAt": "2026-09-01T13:13:59.949Z"
  },
  "logEntries": [                    // CV/LLM-detected actions -> Events tab + seekbar markers
    {
      "id": 1,
      "actionCode": "A2.1",          // must match a RUBRIC relatedActions code to link to Evaluation
      "actionLabel": "Calls the patient's name loudly (audio)",
      "start": "00:00:05",           // HH:MM:SS
      "end": "00:00:05",             // same as start = point marker; end > start+1s = duration marker
      "comment": ""
    }
  ],
  "transcriptSegments": [            // optional — powers the Transcript tab + CC caption overlay
    {
      "id": 1,
      "start": "00:00:05",
      "end": "00:00:07",
      "speaker": "Student",
      "text": "Hi, can you hear me?"
    }
  ],
  "rubricScores": {                  // keyed by rubric criterion id (r1, r2, ..., r4-a, r4-b, ..., r11)
    "r1": {
      "mark": "1",                   // numeric string, matched against that criterion's point value
      "markCorrect": "correct",      // one of: correct | partial | incorrect | notseen
      "comment": "all good"
    }
  },
  "overallComments": ""              // shown as a highlighted summary box above per-criterion feedback
}
```

The 11-criterion / 9.5-point rubric structure (titles, point values, and
`relatedActions` action-code mappings) is hard-coded in the `RUBRIC` array
inside `bls-feedback-viewer.html`, kept in sync with the assessor annotation
tool ([bls-annotation-tool](https://github.com/vanechev/bls-annotation-tool)).
Only the total score and per-criterion written comments are shown to
students — this viewer intentionally does not expose raw assessor scoring
mechanics beyond what's needed for transparent feedback.

## Sample data

`data/BLS_annotation_V1-s1_part1_1788268439950-v2.json` is a real annotation
file (with a small hand-added `transcriptSegments` sample) you can use to
test the viewer. The four matching video files are not included in this
repository — see below.

## Status

Actively evolving research tool — not yet finalized for classroom
deployment. Feedback and iteration ongoing.
