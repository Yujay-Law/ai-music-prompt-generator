# 🎧 AI Music Prompt Generator

Convert audio references (descriptions, existing songs, or structured data) into **optimized prompts** for AI music generation models — Suno, Udio, and beyond.

A [Cowork](https://claude.com/desktop) skill that analyzes reference tracks and generates ready-to-use music prompts.

## Features

- **Song Analysis** — Extract BPM, key, mood, genre, instruments, structure, and production qualities from any reference
- **Suno Prompts** — Descriptive, natural-language prompts optimized for Suno's style-tag format
- **Udio Prompts** — Technical, production-focused prompts with detailed arrangement specifications
- **Remix Variants** — 3-5 alternate versions (genre flips, energy shifts, mood flips, cinematic/orchestral adaptations)
- **Style Transfer** — Blend the structure of one song with the vibe of another
- **Multi-Version Matrix** — Generate prompt variations across energy, mood, genre, and tempo axes

## Installation

Save `SKILL.md` to your Cowork skills directory and the skill will be available automatically.

## Usage

```
"I want to make a song like Blinding Lights by The Weeknd"
"Create a lo-fi remix of a 90s pop song"
"Something like Daft Punk but orchestral"
"Generate 5 variations of a sad piano ballad"
```

The skill will analyze the reference, then generate:
- An audio analysis summary
- A Suno-optimized prompt
- An Udio-optimized prompt
- 3-5 creative remix/alternate prompts

## Input Formats

Accepts a variety of input formats:
- Song name + artist
- Free-text vibe description
- Structured audio JSON
- Partial ideas ("Something like X but Y")

## License

MIT — see [LICENSE](LICENSE) for details.
