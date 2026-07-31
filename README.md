# Cabin 13

A browser-based interactive horror visual novel. Five friends break down at mile
marker thirteen, find a skin-bound book and a crate of tapes in the cellar of an
empty cabin, and read something aloud that they should not have.

**100 chapters**, branching — 91 story chapters across four acts plus 9 endings.

## Playing it

Open `cabin13.html` in any modern browser. That is the whole game: one
self-contained file, no build step, no assets, no network requests.

Sound starts on the first click (browsers require a gesture before audio).
Volume, mute, and a jump-scare kill switch live in the top bar.

- `1` / `2` / `3` — pick a choice
- `Space` — skip the typewriter reveal
- `M` — mute
- `Esc` — menu (saves, chapter select, settings)

## What is in the box

Everything is generated at runtime — there are no images, fonts, or audio files.

- **Audio** is synthesised in WebAudio: a continuous wind/drone bed with
  scheduled creaks, plus one-shot heartbeat, static, whisper, growl, creak,
  drip, impact and sting voices. Buffers are built on first use and cached.
- **Jump-scare imagery** is inline SVG (original designs — twisted grins,
  wrong-angle joints, black eyes), flashed at high contrast.
- **Effects** are CSS keyframes plus three SVG filters: rough/torn display type,
  chromatic aberration, and a `requestAnimationFrame`-driven displacement warp
  for possession beats.

## Structure

The engine knows nothing about the story. Chapters are pure data in a single
`CHAPTERS` array, with a declarative condition/effect language:

```js
{
  id:'c35', act:2, danger:0.7, title:'Someone Here Is Already Gone',
  enter:{ hollow:['@NEXT'], sanity:-12 },
  choices:[
    { label:'Restrain them.', tone:'danger', sound:'wood',
      effect:{ sanity:-5 }, next:'c36' },
    ...
  ]
}
```

- **State** — `{ sanity, survivors, hollowed, dead, inventory, flags, ... }`
- **Conditions** — `flags`, `has`, `hasAny`, `lacks`, `sanityMin/Max`,
  `aliveMin/Max`, `alive`, `dead`, `hollow`, `hollowMin`, `anyHollow`,
  `visited`, `not`
- **Effects** — `sanity`, `noise`, `add`, `remove`, `set`, `unset`, `kill`,
  `hollow`, `save`
- **Routing** — `next`, or a `nextIf` list where the first matching condition
  wins. `@ENDING` hands off to `ENDING_RULES`, which picks the ending from
  accumulated flags.
- **Tokens** — `@NEXT` / `@HOLLOW` / `@ALLHOLLOW` resolve against who is
  actually still alive, so hub chapters read correctly on every branch.
  `{hollow}`, `{alive}`, `{n}` interpolate names into prose at render time.

Full schema notes are in the comment block above the story data in
`cabin13.html`.

Every choice always leads somewhere — some just cost more sanity or more people.
The engine validates the graph on boot and logs dangling links, duplicate ids,
wrong choice counts and unreachable nodes to the console.

## Endings

Survive & Escape · Become the New Host · Burn It All Down · Everyone Dies ·
The Reseal · Dawn Chorus · The Last Kindness · Tape Six · The Salt Circle

## Deploying

Static site, no build. `vercel.json` rewrites `/` to `cabin13.html`, so the root
URL serves the game.

## Content notes

Flashing imagery, sudden loud sounds, implied gore and body horror. Jump scares
can be turned off in settings; there are ten, spaced across the hundred chapters.
