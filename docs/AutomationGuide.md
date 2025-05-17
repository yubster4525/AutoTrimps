# AutoTrimps Automation Guide

This document provides an overview of how the **AutoTrimps** project automates gameplay for the web game *Trimps* and outlines steps to adapt similar automation techniques for other games.

## 1. Overview of AutoTrimps

AutoTrimps is a collection of JavaScript modules that run inside the game's webpage. It injects custom scripts into the page and hooks into Trimps' internal functions and DOM elements to automate every aspect of play. Features include automatic combat, building construction, equipment upgrades, resource gathering, portaling, and more. A separate "Graphs" module records and graphs run statistics using Highcharts.

### Script Loading

- **Tampermonkey Userscript**: `.user.js` loads `AutoTrimps2.js` from GitHub when the browser runs the game. For the Steam version, `mods.js` performs the same injection in the game's `mods` directory.
- **AutoTrimps2.js**: This is the main entry point. It sets the base path for modules and dynamically loads the required files using `ATscriptLoad()`.

```javascript
var ATversion = 'Zek v5.1.0',
    basepath = 'https://Zorn192.github.io/AutoTrimps/',
    modulepath = 'modules/';
ATscriptLoad(modulepath, 'utils');
```

AutoTrimps2.js then calls `initializeAutoTrimps()` which loads the GUI and an array of modules such as `fight`, `buildings`, `gather`, and `perks`. After initialization the script starts `mainLoop()` and `guiLoop()` timers that run automation and update the UI.

### Module Structure

Each module lives under `modules/` and registers itself in the global `MODULES` object. Examples include:

- `fight.js` – automates combat and stance switching.
- `buildings.js` – buys buildings and storage based on configurable thresholds.
- `equipment.js` – upgrades weapons and armor following user settings.
- `portal.js` – triggers a new portal when helium-per-hour drops.
- `performance.js` – enables an AFK overlay to reduce DOM updates while away.

`utils.js` provides helper functions and persistent settings stored in `localStorage` under `autoTrimpSettings`.

### Data Storage and Settings

Player preferences are stored in the browser's `localStorage` so that settings persist across sessions. The GUI defined in `SettingsGUI.js` exposes checkboxes and number inputs for most modules, enabling players to toggle features or set thresholds at runtime.

### Graphs Module

`Graphs.js` loads Highcharts to visualize run history. Data about each portal is compressed with LZString and saved to `localStorage`. Users can view helium gained, run length, map counts, and other metrics from prior runs.

## 2. Automation Techniques Used

AutoTrimps achieves automation by:

1. **Injecting Scripts**: Using a userscript or mod file to append custom `<script>` tags to the game page.
2. **Hooking Game Variables**: Accessing the `game` global object provided by Trimps to read and write values such as resources, combat state, and upgrades.
3. **DOM Manipulation**: Creating custom UI elements to configure options and display information.
4. **Timers and Main Loops**: Running continuous `setInterval` loops (every 100ms by default) to check conditions and issue in‑game commands like `buyBuilding()` or `fightManual()`.
5. **Persistent Settings**: Saving options to `localStorage` so automation preferences reload automatically.
6. **Modular Design**: Splitting features into separate modules so that new functionality can be added or removed easily.

## 3. Adapting This Approach for Another Game

To implement similar automation for a different game, follow these steps:

1. **Determine Injection Method**
   - **Browser Game**: Use a userscript manager like Tampermonkey to load your script when the game is opened.
   - **Desktop Game**: If the game allows mods or custom scripts, place your loader file in the appropriate mod directory.
2. **Analyze Game Internals**
   - Inspect the page's source to locate global objects or functions controlling gameplay.
   - Use browser developer tools to monitor network calls, DOM updates, and in‑game events.
3. **Create a Loader Script**
   - Similar to `AutoTrimps2.js`, create a script that sets your base path and loads individual modules.
   - Ensure modules can be reloaded or updated without refreshing the entire page.
4. **Implement Helper Utilities**
   - Provide a `utils` module to handle settings, persistent storage, and common functions.
5. **Build Feature Modules**
   - For each major aspect of automation (combat, upgrades, resource management), create a dedicated module.
   - Hook into game functions or DOM elements to perform actions automatically. Use timers to check for conditions at regular intervals.
6. **Add a GUI for Settings**
   - Inject HTML elements into the game’s UI so players can toggle features on or off and adjust thresholds without editing code.
7. **Store and Load Preferences**
   - Save configuration values to `localStorage` (or an equivalent storage system) and load them on startup.
8. **Optional Data Logging and Graphs**
   - If the game tracks progress between runs, consider saving stats and plotting them with a library like Highcharts, similar to `Graphs.js`.

## 4. Best Practices and Considerations

- **Respect the Game’s Terms of Service**: Automating gameplay may violate the terms of some games. Always verify permission before distributing or using automation tools.
- **Modularity**: Keep features in separate modules so users can disable parts they do not need.
- **Performance**: Minimize DOM updates and expensive calculations to avoid slowing down the game, especially when running in short intervals.
- **User Feedback**: Provide logs or on-screen indicators so users know what actions are being taken.
- **Updates**: Games may change over time. Organize your code so it is easy to patch when new versions modify internal variables or functions.

By following the structure used in AutoTrimps—script injection, modular design, DOM manipulation, and persistent settings—you can build a robust automation script for many browser-based or scriptable games.

