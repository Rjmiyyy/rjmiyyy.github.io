# CHECK OUT — A Horror Choice Game

`CHECK OUT` is a short browser-based horror story set in Hotel 404. The player makes choices, changes a demon's mood, collects unusual items, and reaches different outcomes and endings.

The project uses only HTML, CSS, and vanilla JavaScript. It does not need a framework, build tool, database, or installation.

## Play the game

Live version: https://rjmiyyy.github.io/game/index.html

To preview it locally, open a terminal in the repository root and run:

```bash
python3 -m http.server 8766 --directory game
```

Then visit http://127.0.0.1:8766/ in a browser. Using a local server is recommended because browsers may restrict sound when `index.html` is opened directly as a file.

Sound starts after the player's first click or key press because modern browsers block autoplay.

## File guide

| File | Purpose |
| --- | --- |
| `index.html` | Page structure, buttons, screens, demon markup, and Halloween decorations |
| `game.js` | English story, choices, routes, endings, game state, mood logic, audio, and controls |
| `i18n.js` | Traditional Chinese translations and Simplified Chinese conversion |
| `style.css` | Main layout, colours, animation, responsive design, and Halloween artwork |
| `final-overrides.css` | Latest visual adjustments; loaded after `style.css`, so its rules take priority |
| `mystery-bgm.mp3` | Background music |
| `bell.wav` | Choice/click sound |
| `outcome-*.wav` | Different sounds for died, escaped, trapped, hired, and erased results |

The script order near the end of `index.html` is important: `i18n.js` must load before `game.js`.

## Change the story or choices

Open `game.js` and find `const scenes`.

Each scene has an ID such as `wake`, `hall`, or `lobby`:

```js
example: {
  chapter: '02 / EXAMPLE',
  time: '03:40 AM',
  icon: '?',
  title: 'Example Scene',
  text: `<p>Your story text goes here.</p>`,
  choices: [
    ['Choice shown to the player', 'nextSceneId', 10, 'optionalItem'],
    ['Another choice', 'endingId', -15]
  ]
}
```

A choice follows this order:

```text
[button text, next scene/ending ID, mood change, optional item or flag]
```

- A positive mood number makes the demon happier.
- A negative mood number makes the demon angrier.
- The optional fourth value can add an item or story flag.
- Use an existing scene ID to continue the story.
- Use an existing ending ID to finish the story.
- Keep each scene to four choices or fewer for the current layout.

When adding a scene, make sure every destination ID exists. A misspelled ID sends the player to the error ending.

## Change or add an ending

Three sections in `game.js` work together:

1. `endings` contains the ending number, icon, title, and full ending story.
2. `resultTypes` contains the immediate status page, such as `DIED!`, `ESCAPED!`, `TRAPPED!`, `HIRED!`, or `ERASED!`.
3. `outcomeSounds` maps a result type to its sound file.

If you add a new ending ID, add the same ID to both `endings` and `resultTypes`. Add its translations in `i18n.js` as well.

The first value in an `endings` entry is zero-based internally: `0` is displayed as `ENDING 1`, `1` as `ENDING 2`, and so on. The game intentionally does not reveal the total number of endings.

## Demon mood and face

The demon starts at `50%`. The `update()` function in `game.js` controls the mood ranges:

| Mood | State | Face class |
| --- | --- | --- |
| 80–100 | DELIGHTED | `happy` |
| 62–79 | AMUSED | `happy` |
| 38–61 | CURIOUS | `happy` |
| 18–37 | ANNOYED | `annoyed` |
| 0–17 | FURIOUS | `furious` |

The matching face designs are in CSS under `.demon-face.happy`, `.demon-face.annoyed`, and `.demon-face.furious`. Only `furious` uses the hostile eyes, strong angry eyebrows, and clenched teeth.

Some routes check the exact mood before choosing an ending. Search `game.js` for `state.mood` before changing the thresholds.

## Edit translations

English text is stored mainly in `game.js`. Traditional Chinese is stored in the `ZH` object in `i18n.js`.

When English content changes:

1. Update the English scene or ending in `game.js`.
2. Update the matching scene or ending ID in `i18n.js`.
3. Keep translated choice arrays in the same order as the English choices.

Simplified Chinese is generated from the Traditional Chinese content by the `simp()` conversion function. If a word is not converted correctly, add it to the replacement map in that function.

## Change sounds and volume

Audio setup is near the top of `game.js`:

```js
bgm.volume = .9;   // 90% background music
bell.volume = 1;  // 100% click effect
```

During a result sound, the BGM and click effect temporarily drop to `18%`, then return to their normal levels. Search for `restoreGameVolumes()` and `bgm.volume=.18` to change this behaviour.

To replace audio, keep the same filenames or update the paths in `game.js`. Use audio that your group has permission to publish.

## Change the design

- Global colours are CSS variables at the beginning of `style.css`.
- The orange Halloween accent is `--halloween`.
- Homepage decorations use `.graveyard`, `.iron-fence`, `.ground-mist`, `.hanging-spider`, and `.jack-lantern`.
- Mobile rules use `@media(max-width:780px)`.
- `final-overrides.css` loads last and may override values from `style.css`; check it if a style change appears to have no effect.

The Halloween elements are CSS artwork rather than emoji or image files.

## Safe editing checklist

After making changes:

1. Run `node --check game/game.js` to catch JavaScript syntax errors.
2. Start the local server and play through every changed route.
3. Test English, Traditional Chinese, and Simplified Chinese.
4. Check both desktop and mobile widths.
5. Confirm sound after clicking the page once.
6. Confirm every new choice reaches a valid scene or ending.
7. Hard-refresh the live page after publishing if an old cached version appears.

## Publishing with GitHub Pages

The live game is served from the `game/` folder of the `Rjmiyyy/rjmiyyy.github.io` repository. Push updated files to the repository's `main` branch. GitHub Pages will deploy automatically, usually within a few minutes.

Do not commit passwords, access tokens, or other secrets to this folder.
