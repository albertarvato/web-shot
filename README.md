# Web Shot

A Google Chrome extension that captures **the entire web page** with one click:
it scrolls the page, stitches the frames into a single PNG, saves the file to Downloads
and copies the image to the clipboard.

## Features

- Full-page screenshots by scrolling, at full screen resolution (respects HiDPI and page zoom).
- Files are saved automatically to Downloads as `screenshot_<domain>_<date>_<time>.png`.
- Images are copied to the clipboard, ready to paste into a chat or an editor.
- Fixed headers appear only at the beginning of the screenshot.
- Floating banners, chat buttons and cookie bars appear only at the end.
- Sticky elements appear once, in their own place.
- Progress and results appear on the icon badge: `%`, then `OK` or `ERR`. Error details appear in the icon tooltip.
- Start by clicking the icon or pressing **Ctrl+Shift+S** (**Command+Shift+S** on macOS).
- English and Russian UI, following the browser language.

## Installation

### From a release

1. Download `web-shot-<version>.zip` from a release you trust and unzip it into a permanent folder.
2. Open `chrome://extensions` and enable **Developer mode**.
3. Click **Load unpacked** and select the unzipped folder.
4. Pin the icon to the toolbar using the puzzle icon and the pin next to Web Shot.

### From source

```bash
git clone https://github.com/albertarvato/web-shot.git
```

Then follow steps 2-4 above, selecting the `web-shot` folder.

## Usage

Open the page and click the Web Shot icon or press **Ctrl+Shift+S**. Do not switch tabs or scroll while it is capturing.
When the badge shows `OK`, the download has been requested and the image is on the clipboard.

Change the shortcut at `chrome://extensions/shortcuts`. If Ctrl+Shift+S is already
taken by another extension, Chrome will not assign it; set your own combination there.

To capture local files (`file://`), enable **Allow access to file URLs** on the extension's
card at `chrome://extensions`.

## How it works

1. A script is injected into the page to hide scrollbars, disable smooth scrolling and temporarily make sticky elements static.
2. The page is scrolled one screen at a time; each screen is captured with `chrome.tabs.captureVisibleTab` (at most twice per second, a Chrome limit).
3. Frames are stitched on an `OffscreenCanvas` in the background service worker.
4. The PNG is copied to the clipboard via the Clipboard API and saved via `chrome.downloads`.
5. Temporary page changes are reverted and the scroll position is restored, including when capture fails, as long as the page is still accessible.

## Permissions

| Permission | Purpose |
|---|---|
| `activeTab` | Temporary access to the current tab when you invoke the extension |
| `scripting` | Scrolling the page and hiding fixed elements during capture |
| `downloads` | Saving the PNG to Downloads |
| `clipboardWrite` | Copying the image to the clipboard |

## Privacy and safety

- The current source processes screenshots locally and contains no analytics or screenshot-upload code.
- Its `fetch` call reads a local `data:` URL returned by Chrome's capture API, not a remote server.
- Scrolling can trigger the website's own network requests, such as loading images or additional content.
- Screenshots can contain sensitive information. They are saved to Downloads and replace the clipboard contents; other software or cloud-sync settings may expose those copies.
- The extension does not request persistent access to all websites, cookies or browsing history.
- Review the source you install. These statements do not verify separately downloaded release archives or future changes.

## Limitations

- Chrome restricts capture or script injection on internal pages (`chrome://`), the Chrome Web Store and the built-in PDF viewer; these may show `ERR`.
- If the page scrolls an inner container instead of the document (Gmail, some web apps), only the visible area is captured.
- Horizontal scrolling is not captured, only the window width.
- Very long pages are cut at 60,000 CSS pixels; very large images are scaled down proportionally to fit canvas limits.
- Animated or infinitely loading content may not stitch perfectly.
- Keep the captured tab active throughout capture. Chrome captures the active tab in the window, and switching tabs during a capture can race with the extension's active-tab check.

## Building a release archive

```bash
./scripts/package.sh   # creates dist/web-shot-<version>.zip
```

Requires Bash, Python 3 and `zip`. No third-party packages need to be installed.

## Authors

- **Black Triangle** ([@HelpFreedom](https://github.com/HelpFreedom)): original author
- **Claude** (Anthropic): code co-author

## License

[GNU General Public License v3.0](LICENSE)
