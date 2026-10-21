# Contributing to Powder Box

Thanks for your interest in improving Powder Box. Bug reports, ideas and pull requests are all welcome.

## Running the project

The game is a single file, so there is nothing to install or build. Open `index.html` in a browser and refresh after each change.

## Reporting a bug

Open an issue and include:

- What you expected to happen and what happened instead
- The elements involved and the steps to reproduce, for example "dropped lava on oil next to a wall"
- Your browser, operating system and device (desktop, phone or tablet)

## Suggesting an element or feature

Open an issue describing the idea and how it should interact with the existing elements. Reactions that follow simple, familiar rules work best, for example "acid dissolves metal".

## Submitting a pull request

1. Fork the repository and create a branch for your change.
2. Keep the project dependency-free and in a single HTML file unless there is a strong reason to change that.
3. Match the existing code style and keep the simulation fast. The game targets 60 frames per second on a typical laptop.
4. Test in your browser: draw every element you touched and check its reactions. If you changed input handling, also try resizing the window and drawing by touch.
5. If you add an element, follow the steps in "Hacking on it" in the [README](README.md) and add it to the element and reaction tables.
6. Add a line to the Unreleased section of the [CHANGELOG](CHANGELOG.md).
7. Open the pull request and describe what changed and why.

By contributing, you agree that your contributions are released under the project license.
