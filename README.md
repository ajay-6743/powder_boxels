# Powder Box

A falling-sand physics sandbox that runs entirely in the browser. Pour powders, liquids and gases onto the canvas, heat or freeze them, and watch them melt, boil, burn, explode and react. There are more than 5,700 elements, from sand and water to the full periodic table, salts, organic compounds, alloys, minerals, animals, plants and food.

No build step, no dependencies, no server needed.

## Run it

Open `index.html` in a modern browser.

If you would rather use a local server, any static one works:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

To publish it, push the repo to GitHub and turn on **Settings → Pages → Deploy from a branch → `main` / `(root)`**.

## Using it

- Pick a category tab, pick an element, then paint on the canvas. **Filter** narrows long categories.
- The tabs are Powders, Liquids, Solids, Gases, Special, Life, Food, Elements, Salts, Organic, Minerals, Alloys and Chemicals.
- **Heat** and **Cold** change the temperature of whatever you paint over. **Erase** clears cells.
- **Brush** sets the brush size, **Pause** / **Play** freezes the simulation, **Clear** empties the canvas.
- **Save** and **Load** use three slots, chosen with the dropdown, kept in the browser's `localStorage`.
- **Lookup** searches every element and describes how it behaves: its state, density, melting, boiling and ignition points, what it turns into, and what it reacts with. Click a result to select it.

Light and dark themes follow the system setting.

## Project structure

```
index.html                 page markup; loads the scripts in dependency order
css/styles.css             styles and theme variables
js/
  util.js                  $ and rnd shorthands
  data/                    raw tables, as template strings
    elements.js              base elements
    reactions.js             base reactions
    periodic-table.js        all 118 chemical elements with melting points, boiling points and densities
    special-elements.js      hand-made specials, chemicals and body parts
    extra-reactions.js       more hand-written reactions
  generators/              code that expands the data into thousands of elements
    helpers.js               shared helpers and the lists they fill
    periodic-table.js        solid, liquid and gas forms, radioactive decay, metal powders
    salts.js                 oxides, hydroxides, carbonates, nitrates... of every metal
    organic.js               organic families, one to thirty carbons
    alloys.js                every pair of 26 metals, plus named alloys
    minerals.js              minerals, gems and rocks
    life.js                  creatures, trees and plants
    food.js                  raw, cooked, burnt and rotten food
    special.js               registers the special elements and adds "<Name> Source" blocks
  engine/
    tables.js                master element list and the typed property arrays
    reactions.js             reaction lookup: base table, extra table and generated rules
    state.js                 grid arrays and shared state
    simulation.js            per-cell rules and step()
    renderer.js              colours, draw() and fit()
  ui/
    input.js                 painting with the pointer
    palette.js               tools, tabs and the element palette
    lookup.js                the Lookup dialog and element descriptions
    controls.js              brush, pause, clear, save and load
  main.js                  start-up and the animation loop
```

The scripts are plain `<script>` tags rather than ES modules, so they share one global scope and the **load order in `index.html` matters**. This is also what lets the page work when opened straight from disk. A few lists built by the generators (`MT`, `ST`, `CRE`, `PLN`, `PER`) are read again by `engine/reactions.js`.

### How it fits together

1. `data/` holds the raw tables.
2. `generators/` expand them into thousands of extra element lines.
3. `engine/tables.js` joins every line into one element list and parses each element's options into typed arrays.
4. `engine/reactions.js` builds the reaction lookup.
5. `engine/state.js`, `simulation.js` and `renderer.js` run and draw the grid.
6. `ui/` and `main.js` wire it to the page.

## Data formats

### Elements

One element per line:

```
name|color|kind|density|options|category
```

- **color**: six hex digits, no `#`.
- **kind**: `1` powder, `2` liquid, `3` gas, `4` solid.
- **density**: heavier things sink through lighter liquids and gases. Leave it blank for solids. Gases lighter than 1 rise; heavier ones sink and spread.
- **options**: space-separated codes, listed below.
- **category**: optional palette tab. Without it the tab comes from the kind, or from the name for a few special cases.

Example from `js/data/elements.js`: `Gunpowder|55555e|1|5.2|i250 b8`

| Option | Meaning |
|---|---|
| `T<n>` | Starting temperature in °C (default 20) |
| `K<n>` | Held temperature: the cell is forced to n °C every update (heaters, freezers) |
| `h<n>>Name` | Above n °C it turns into Name |
| `c<n>>Name` | Below n °C it turns into Name |
| `i<n>` or `i<n>>Name` | Ignites above n °C, into fire or into Name |
| `b<n>` | Burn time of the fire it leaves behind |
| `x<n>` | Explodes with blast radius n when ignited |
| `L<n>>Name` | Lifetime in updates, then turns into Name (or vanishes if no name) |
| `f<n>` | Liquid flow: below 1 is the chance to move each update (thick liquids); 1 or more is how far it spreads sideways. Default 5 |
| `s>Name` | Emits Name below it (above it for gases) |
| `C` | Cloner: copies a touching element |
| `V` | Void: deletes whatever touches it |
| `P` | Plant: spreads into adjacent water |
| `A` | Acid: dissolves powders and solids that aren't resistant |
| `r` | Resistant to acid, voids and explosions |
| `w1` `w2` `w3` | Creature that walks, swims or flies |
| `d<p>>Name` | Radioactive decay: chance p per update to become Name, adding heat |
| `g<p>>Name` | Ageing: chance p per update to become Name |

In `>Name` targets, spaces become underscores: `h1700>Molten_Glass`.

### Reactions

One reaction per line:

```
A+B>C+D chance
```

When A touches B, A becomes C and B becomes D, with that chance per update. An empty name clears the cell, so `Water+Salt>Salt Water+ .3` turns the water into salt water and removes the salt. Both orders are registered, and a pair that already has a reaction keeps its first definition.

## Adding your own

1. **An element:** add a line to `js/data/special-elements.js` (just before the closing backtick), for example `Pink Goo|ff4fa3|2|3.4|f.3|Special`.
2. **A reaction:** add a line to `js/data/extra-reactions.js`, for example `Pink Goo+Salt>Slime+ .1`.
3. Reload the page. The new element appears in its tab and in Lookup.

**Heads-up about saves.** A saved slot stores each cell's element as its position in the generated list. Adding or reordering elements shifts those positions, so older saves can load as different elements. Appending to the end of `js/data/special-elements.js` disturbs the fewest of them (only the `Source` blocks that follow).
