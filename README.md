# ai-proving-grounds

## Setup

This project uses **Python 3.12** (pydub and its dependency `audioop` do not work on 3.13).

```bash
uv sync
```

If you don’t have 3.12: `uv python install 3.12`, then `uv sync`.

### System dependency: ffmpeg

Notebooks that use **yt-dlp** or **pydub** (e.g. YouTube/audio loading) need **ffmpeg** and **ffprobe**. They are system binaries, not Python packages—install them with Homebrew:

```bash
brew install ffmpeg
```

Then re-run the notebook; postprocessing (e.g. extracting audio) will work.
