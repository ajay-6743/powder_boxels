# Powder Boxels

A falling-sand sandbox game for the browser, inspired by Sandboxels. Pour sand, water, lava and acid, start fires, blow things up, and watch 22 elements react to each other in real time.

The whole game is one HTML file. No build step, no dependencies, no network requests.

## Features

- 22 elements plus an eraser, grouped into powders, liquids, solids and gases
- Density-based physics: sand sinks through water, oil floats on water, gases rise
- Chain reactions: fire spreads through wood, plants, oil, gas and gunpowder, and TNT sets off nearby TNT
- Living plants that grow by drinking water
- Mouse and touch drawing with an adjustable brush, plus pause and clear
- The world resizes with the window and keeps what you built
- Light and dark interface that follows your system setting

## Play

**Locally:** download the file and open it in any modern browser. No server needed.

## Controls

| Action | How |
| --- | --- |
| Choose an element | Click or tap a button in the palette at the bottom |
| Draw | Click and drag, or touch and drag. Hold still to keep pouring |
| Brush size | Slider in the header, from 1 to 12 |
| Erase | Select **Erase** and draw over anything |
| Pause or resume | **Pause** / **Play** button. You can still draw while paused |
| Wipe the world | **Clear** button |

Drawing only fills empty cells, so use Erase to make room. Powders, liquids and gases are sprinkled as you draw, while solids fill the whole brush.

## Elements

| Family | Element | Behavior |
| --- | --- | --- |
| Powder | Sand | Piles up and sinks through liquids. Turns to glass next to lava |
| Powder | Dirt | Heavy powder |
| Powder | Snow | Light powder that floats on water and melts near fire or lava |
| Powder | Gunpowder | Burns in a rapid chain reaction when touched by fire or lava |
| Powder | Ash | Light powder left behind by burning |
| Liquid | Water | Flows, puts out fire, feeds plants, boils into steam |
| Liquid | Oil | Floats on water and burns |
| Liquid | Acid | Dissolves most solids and powders, and is slowly used up doing it |
| Liquid | Lava | Thick and slow. Ignites things, melts ice and snow, makes glass from sand |
| Solid | Stone | Forms when lava cools in water. Dissolves in acid |
| Solid | Wall | Indestructible, even by TNT |
| Solid | Wood | Burns for a long time |
| Solid | Plant | Grows into nearby water. Flammable |
| Solid | Ice | Melts into water near fire or lava |
| Solid | Glass | Made from sand and lava |
| Solid | TNT | Explodes when touched by fire or lava. Walls survive the blast |
| Solid | Faucet | Pours water downward |
| Solid | Void | Deletes anything touching it, except walls, faucets and other voids |
| Gas | Fire | Burns for a short time, spreads to flammable neighbors, leaves smoke and sometimes ash |
| Gas | Smoke | Rises and fades away |
| Gas | Steam | Rises, then condenses back into water |
| Gas | Gas | Rises and lingers. Burns almost instantly when lit |

## Reactions at a glance

| Combination | Result |
| --- | --- |
| Fire + Water | Steam, and the fire goes out |
| Lava + Water | Stone and steam |
| Lava + Sand | Glass, gradually |
| Fire or Lava + Snow or Ice | Water |
| Fire or Lava + Wood, Plant, Oil, Gas, Gunpowder | Ignites |
| Fire or Lava + TNT | Explosion with a radius of about 8 cells |
| Acid + Sand, Dirt, Snow, Gunpowder, Ash, Stone, Wood, Plant, Ice, Glass, TNT | Dissolves them |
| Plant + Water | More plant |

## Things to try

- Pour oil onto water, then drop a bit of fire on the oil.
- Build a basin from Wall, fill it with water, and plant a few Plant cells at the edge.
- Pack TNT inside a Wood box and light it, or line up TNT blocks to watch the chain.
- Drop Lava onto Water for stone and a cloud of steam, or onto Sand for glass.
- Put a Faucet above a basin with a Void at the bottom for a fountain that never floods.
- Seal Gas inside a Wall chamber, then open a gap and add fire.

## How it works

- The world is a grid of cells, 4 px each (3 px on narrow screens), stored in typed arrays: element type, a life timer, a color shade and a moved-this-frame stamp.
- Each frame updates cells from bottom to top, flipping between left-to-right and right-to-left scans to avoid directional bias.
- Powders and liquids fall by swapping with lighter cells below, using a density table. Liquids also spread sideways.
- Gases rise and drift. Fire, smoke and steam count down a life timer.
- Neighbor checks drive the reactions: burning, melting, dissolving and growing.
- Explosions are queued and resolved after the main pass, which lets TNT trigger chains.
- Rendering writes straight into an `ImageData` pixel buffer, and CSS scales the canvas with `image-rendering: pixelated`.

## Hacking on it

Elements are defined in the `E` array at the top of the script. Density (`D`), flammability (`FL`) and burn time (`BL`) are lookup tables indexed by element id, and behavior lives in `upd` and `react`.

To add an element:

1. Append it to the end of `E`. Ids are positions, so inserting in the middle shifts everything after it.
2. Add an entry to `D`, and enlarge `FL` and `BL`, which are sized for 23 entries.
3. Add its behavior to `upd` or `react`.

## Ideas for later

- Heat and cold tools
- Save and load worlds
- Simulation speed control
- More elements such as metal, salt, mud and seeds
