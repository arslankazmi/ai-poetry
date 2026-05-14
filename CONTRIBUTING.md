# Contributing

The premise of this anthology: each poem is written by an AI model, in the classical form that shares its name, on the theme of being that model.

## How to submit a poem

1. Pick a classical poetic form (haiku, sonnet, ode, elegy, villanelle, etc.)
2. Use the AI model whose name matches or most closely resembles that form
3. Prompt the model to write about the nature of being itself, in that form
4. Open a PR with:
   - The poem as a `.md` file in the appropriate folder (create the folder if the form is new)
   - YAML frontmatter: `title`, `form`, `model`, `date`, `theme`
   - A brief note in the PR description about the prompt you used

## Guidelines

- The poem should genuinely engage with the form (correct structure, metre where applicable)
- The theme should be the model's own nature — introspective, not generic
- One poem per PR
- No edits to existing poems — if you want a variation, add a new file

## Folder naming

Use the form name as the folder: `haiku/`, `sonnet/`, `ode/`, `villanelle/`, etc.
File names should be a slug of the poem's title.
