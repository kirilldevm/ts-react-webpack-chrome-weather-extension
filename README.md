# Weather Extension

A Chrome extension that shows current weather for your home city and any cities you add. Built with React, TypeScript, and Webpack (Manifest V3).

## Features

- **Popup** — View weather cards for your home city and saved cities; add cities by name; switch between Celsius and Fahrenheit; toggle a page overlay.
- **Options page** — Set your home city and enable auto-show overlay on every page load.
- **Page overlay** — Floating weather card on any site (manual toggle from the popup or automatic via options).
- **Toolbar badge** — Home city temperature on the extension icon, refreshed hourly.
- **Context menu** — Select text on a page and add it as a city via _Add city to weather extension_.

Weather data comes from the [OpenWeatherMap Current Weather API](https://openweathermap.org/current).

## Tech stack

- React 17 + TypeScript
- Material UI v4
- Webpack 5
- Chrome Extension Manifest V3 (service worker background, content scripts, `chrome.storage`)

## Prerequisites

- [Node.js](https://nodejs.org/) (LTS recommended)
- Google Chrome
- A free [OpenWeatherMap API key](https://openweathermap.org/api)

## Getting started

### 1. Install dependencies

```bash
npm install
```

### 2. Configure the API key

Open `src/utils/api.ts` and replace the placeholder with your OpenWeatherMap key:

```ts
const OPEN_WEATHER_API_KEY = 'uses your api from .env file';
```

Do not commit a real API key to a public repository. For production, consider loading the key from environment variables at build time.

Create the file '.env' and pass there your OPEN_WEATHER_API_KEY

```env
OPEN_WEATHER_API_KEY=your-api-key
```

### 3. Build the extension

**Development** (watch mode, faster rebuilds):

```bash
npm start
```

**Production** (minified output):

```bash
npm run build
```

Both commands write bundled files to the `dist/` folder.

### 4. Load in Chrome

1. Open `chrome://extensions/`
2. Enable **Developer mode** (top right)
3. Click **Load unpacked**
4. Select the `dist` folder (not the project root)

After code changes, click **Reload** on the extension card in `chrome://extensions/`, or remove and load `dist` again if something looks stale.

## Usage

| Area             | How to use                                                                                                                                                                                 |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Popup**        | Click the extension icon. Set home city in options first. Use **+** to add cities, the temperature button to toggle °C/°F, and the overlay button to show/hide weather on the current tab. |
| **Options**      | Right-click the extension icon → **Options**, or open `chrome://extensions/` → **Details** → **Extension options**. Set home city, optional auto-overlay, then **Save**.                   |
| **Overlay**      | Shows your home city weather in a card on the page. Delete on the card hides the overlay for that session.                                                                                 |
| **Context menu** | Highlight a city name on any page → right-click → **Add city to weather extension**.                                                                                                       |

On first install, the extension resets saved cities and default options (metric scale, no auto-overlay, empty home city).

## Project structure

```
src/
├── background/       # Service worker: alarms, badge, context menu, install defaults
├── contentScript/    # In-page overlay UI
├── popup/            # Toolbar popup UI
├── options/          # Settings page
├── components/
│   └── WeatherCard/  # Shared weather card (OpenWeather fetch + display)
├── static/
│   └── manifest.json # Extension manifest (copied to dist/)
└── utils/
    ├── api.ts        # OpenWeather API client
    ├── storage.ts    # chrome.storage.local helpers
    └── messages.ts   # Popup ↔ content script messaging
```

Webpack entry points: `popup`, `options`, `background`, `contentScript`. Static assets from `src/static/` (icons, manifest) are copied into `dist/`.

## Scripts

| Command         | Description                                       |
| --------------- | ------------------------------------------------- |
| `npm start`     | Webpack watch build (development config)          |
| `npm run build` | Webpack watch build (production config, minified) |

Install additional dev dependencies with:

```bash
npm install --save-dev <package-name>
```

## Publishing to the Chrome Web Store

1. Run `npm run build` and verify the extension in Chrome using the `dist` folder.
2. Zip the **contents** of `dist` (e.g. `manifest.json`, `popup.html`, scripts, icons at the root of the archive).
3. Upload the zip on the [Chrome Web Store Developer Dashboard](https://chrome.google.com/webstore/devconsole).

Update `version` in both `package.json` and `src/static/manifest.json` before each release.

## Build notes

- Paths in `manifest.json` refer to files in `dist/` (flat layout). Image references use names like `icon.png`, not nested `../static/` paths.
- TypeScript/CSS imports in source files should stay relative; Webpack resolves the dependency graph.
- `contentScript` is excluded from code splitting so it bundles independently for injection into web pages.

## Permissions

Defined in `src/static/manifest.json`:

- `storage` — Saved cities and user options
- `alarms` — Hourly badge temperature updates
- `contextMenus` — Add selected text as a city

Content scripts run on `<all_urls>` to support the overlay on any site.

## License

See repository or author metadata in `package.json` if applicable.
