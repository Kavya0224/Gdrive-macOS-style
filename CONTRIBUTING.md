# Contributing

Thanks for your interest in **GDrive macOS style**! Contributions of any size are welcome: bug reports, ideas, docs and code.

## Running locally

There is no build step.

1. Clone the repo.
2. Open `chrome://extensions` → turn on **Developer mode** → **Load unpacked** → select the `extension/` folder.
3. Open Google Drive and click the **Columns** button next to List/Grid.
4. After editing `extension/content.js` or `content.css`, click the reload icon on the extension card and refresh the Drive tab.

## How it works (short version)

The extension uses no API and no credentials. It reads items from Drive's own list and forwards selection, menus and
shortcuts to the native list, which keeps running (hidden) underneath the columns. See the README for details on
drag and drop and moving files.

Because Drive's HTML changes without notice, the extension checks on load that the expected elements exist and turns
itself off with a notice if they don't. A self-test is available at `…/drive/my-drive#gdf-teste`.

## Where to start

- Browse issues labeled [`good first issue`](../../labels/good%20first%20issue) or [`help wanted`](../../labels/help%20wanted).
- See [roadmap.md](roadmap.md) for planned work and things that still need testing.
- Testing on setups the maintainer doesn't have (dark theme, Shared drives, other languages, multiple accounts) is a
  very valuable contribution, even without writing code.

## Pull requests

- Keep each PR focused on one change.
- Test with **throwaway files** whenever your change touches moving, deleting or uploading.
- Describe what you tested and in which Drive section (My Drive, Shared with me, Computers…).
- Include a screenshot or short GIF for visual changes.
- Update the README and roadmap if behavior changes.

## Reporting bugs

Use the bug report template and include your Chrome version, the Drive section, the language of Drive and any console
errors (DevTools → Console, filter by `gdf`).

## License

By contributing, you agree that your contributions are licensed under the [MIT License](LICENSE).
