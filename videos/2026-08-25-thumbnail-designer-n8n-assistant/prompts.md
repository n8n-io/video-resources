# Prompts

## Build prompt (given to the n8n Assistant)

```
Build an n8n workflow that acts as a thumbnail designer.

## Google Drive structure

- A `people` folder containing one subfolder per person, named after them (e.g. "Leo", "Liam", "Jamie"). Each subfolder holds that person's photos.
- A `references` folder containing thumbnail reference images, identified by name/number.
- A `generated` folder where all output thumbnails are saved.

The folder names are not fixed. The workflow must always list the available folders dynamically instead of relying on hardcoded names or IDs.

## Input

A Form node with a single textarea where the user writes a request in natural language, for example:

`Thumbnail for Leo, use reference 253. Text: "This is a test"`

## AI agent behavior

From that input, the agent should:

1. Parse the target person, the reference identifier, and the exact text to render on the thumbnail.
2. Search Google Drive for the matching person folder and pull their image.
3. Search the `references` folder for the matching reference image.
4. Pass both images as context to the image generation step.
5. Build the generation prompt so the requested text is rendered exactly as written, preserving casing and punctuation.

## Image generation

- Model: `gpt-image-2` via OpenRouter.
- Generate 2 variations per run.
- Output must follow YouTube thumbnail specs: 1280x720 (720p), 16:9 aspect ratio.
- Include explicit prompt instructions for thumbnail composition to be similar to the reference image.

## Output handling

Upload both generated images to the `generated` folder, named using the pattern:

`text_person_date_version`

Example: `this-is-a-test_leo_2026-08-23_v1`

## Validation and fallback

If the agent cannot resolve the person, the reference, or the text, it must not guess. Instead it should stop and return a message to the user stating exactly what is missing and what it needs in order to proceed.
```

## Example form input

```
Thumbnail for Leo, use reference 253. Text: "This is a test"
```
