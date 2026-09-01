# SocialDirector Dataset — Annotation Format

The dataset contains **149 five-second multi-person clips** from three source datasets:

| `dataset` | Clips | Global `id` range | Domain |
|-----------|-------|-------------------|--------|
| `meld` | 19 | 0–18 | TV-series multi-party conversations |
| `mmsi` | 50 | 19–68 | Real-world social interactions (Ego4D & YouTube) |
| `socialgesture` | 80 | 69–148 | Social game recordings |

Videos and first frames are hosted on Hugging Face:
[`oyly/SocialDirector-Dataset`](https://huggingface.co/datasets/oyly/SocialDirector-Dataset).
This directory mirrors the annotation file only.

## `annotations.json`

A list of 149 records, one per clip:

```jsonc
{
  "id": 0,                          // global sample id (0–148)
  "dataset": "meld",                // source dataset
  "video": "videos/meld_0000.mp4",  // 5s ground-truth clip (in the HF dataset)
  "first_frame": "first_frames/meld_0000.jpg",  // reference image for generation
  "prompt": "There are 3 people in the scene: ...",  // structured generation prompt
  "n_speakers": 3,                  // number of annotated persons
  "speaker_boxes": [                // indexed by speaker id (speaker i = speaker_boxes[i]);
    [0.17, 0.22, 0.44, 1.0],        //   [x1, y1, x2, y2], normalized to [0, 1].
    ...                             //   Mostly left-to-right, but the authoritative
  ],                                //   position naming is in `prompt`.
  "speaker_text_ids": [             // per person: [char_start, char_end) span of
    [144, 201],                     //   their action sentence(s) in `prompt`
    ...
  ],
  "speaker_scene_ids": [            // per person: [char_start, char_end) span of
    [33, 67],                       //   their mention in the scene description
    ...
  ],
  "events": [                       // timestamped social events
    {
      "start": 0.0,                 // seconds
      "end": 5.0,
      "speaker": 1,                 // actor index (who)
      "event": "speak with anger",  // action (what)
      "target": "speaker 0 and 2"   // interaction target (toward whom); "" if none
    },
    ...
  ],
  "interactions": [                 // directed actor→target pairs (index form)
    { "speaker": 1, "target": 0, "t": [0.0, 1.0] }  // t: normalized time window
  ]
}
```

**Statistics:** 674 annotated persons (1–8 per clip, mean 4.5) and 479 events, of
which 299 have an explicit interaction target.

**Notes:** single-person clips use the prompt form `"A person is in the scene. ..."`
and carry an empty `speaker_scene_ids` span (`[0, 0]`). Self-directed events
(e.g., a player pointing at themselves) are valid and appear in a few clips.

## Usage & License

This dataset is released **for non-commercial academic research use only**: the
annotations are provided for academic research use only, and the video content
remains the property of its original copyright holders and source datasets
(MELD, Ego4D, SocialGesture / YouTube) — using it requires complying with their
respective terms in addition to ours. Takedown requests: oyly@iis.u-tokyo.ac.jp.
