# Run Layers

A small offline web app that tells you what to wear on a run. Enter the conditions, or tap **Use current weather**, and it gives you a head-to-toe outfit based on how warm you will feel once you are moving.

It is a single static page. There is no build step, no account, and no server. Your settings stay on your phone.

## How it works

The air temperature is not what you dress for. Running warms you up, so the app converts the conditions into a **run-feel temperature** and reads the outfit chart against that.

```
run-feel = feels-like + effort + sun - rain/snow + your tendency
```

| Piece | What it does | Default |
| --- | --- | --- |
| Feels-like | Wind chill when air is 50°F or colder and wind is 3 mph or more. Heat index when air is 80°F or warmer. Otherwise the air temperature. | NWS formulas |
| Effort | How much running warms you above the air. | Easy +12°, Long +18°, Workout +20°, Race +20° |
| Sun | Added when it is sunny. | +5° |
| Rain / snow | Subtracted, because wet clothes chill you. | Rain -5°, Snow -3° |
| Your tendency | Added or subtracted if you run warm or cold. | ±5° |

The effort offsets follow the common "add 10 to 20 degrees" rule of thumb for running. The sun, rain, snow, and tendency values are starting points. Adjust them under **Tune the math** until the chart matches how you actually feel.

### Outfit chart (by run-feel)

| Run-feel | Outfit |
| --- | --- |
| 70° and up | Singlet, short shorts |
| 60-69° | Singlet or tee, shorts |
| 50-59° | Short sleeve, shorts |
| 40-49° | Long sleeve, shorts (light tights near 40°) |
| 30-39° | Long sleeve, tights, gloves, headband |
| 20-29° | Two layers, tights, gloves, hat or headband |
| 10-19° | Two layers and a windbreaker, mittens or gloves, hat |
| 0-9° | Shell and wind pants, mittens, hat and face cover |
| Below 0° | Full winter kit |

Extra tips appear when they apply: rain gear, traction in snow, reflective gear in the dark, wind direction, heat precautions, a throwaway layer for race starts, and a spare layer for long runs. A warning appears when the wind chill is in frostbite territory.

## Live weather

The **Use current weather** button asks for your location, then requests current conditions from [Open-Meteo](https://open-meteo.com/) and fills in temperature, wind, humidity, light (sunny, overcast, dark), and precipitation. You can still change any value afterward.

- Location is rounded to about a kilometer before it is sent. Nothing is stored or logged by the app.
- "Fill automatically when I open the app" only runs once location access has already been granted. iOS may ask for permission again each time a home-screen app opens.
- Offline, the button shows a message and you can enter the weather by hand.
- Open-Meteo is free for non-commercial use and asks for attribution, which the app includes.

The app does not read Apple's Weather app. Apple only offers its weather data to developers through WeatherKit, which needs a paid developer membership and a server.

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The whole app: markup, styles, and logic |
| `manifest.webmanifest` | Name, icons, and display settings for installing to the home screen |
| `sw.js` | Service worker that caches the app so it works offline |
| `icon-192.png`, `icon-512.png`, `icon-maskable-512.png`, `apple-touch-icon.png` | App icons |

## Host it on GitHub Pages (free)

Installing and offline use both require the app to be served over HTTPS, so it needs to be hosted once.

1. Create a new **public** repository on GitHub. Free Pages only works on public repos.
2. Upload every file in this folder to the **top level** of the repo. Do not put them in a subfolder.
3. Go to **Settings → Pages**. Under **Build and deployment**, set Source to **Deploy from a branch**, Branch to **main**, and the folder to **/ (root)**, then save.
4. After a minute or two, the site is live at `https://YOUR-USERNAME.github.io/REPO-NAME/`.

Netlify Drop (app.netlify.com/drop) also works. Drag the folder onto the page and sign in with a free account to keep the site.

## Install on iPhone

1. Open the live address in **Safari**. Other browsers cannot add web apps to the home screen.
2. Tap **Share**, then **Add to Home Screen**.
3. Launch it once from the home screen while online so it can cache everything.

After that it works with no connection. Only the Google font download and live weather need a signal. Without the font cached, the app uses a similar system font.

## Updating

Upload the changed files to the repo and replace the old ones. The app checks for a new version in the background. If you still see the old one, close the app fully and reopen it with a connection. When you change `index.html` or other cached files, bump `VERSION` at the top of `sw.js` (for example `run-layers-v3`) so installed copies drop the old cache.

## Customizing

- **Outfit bands**: edit the `BANDS` array near the top of the script in `index.html`.
- **Defaults for the math**: edit `DEF_TUNE`, or just change the values in the app. They are saved on the device.
- **Extra tips**: edit `extrasFor`.
- **Weather mapping**: edit `weatherToInputs`.

The calculator logic lives between the `/*CALC*/` and `/*ENDCALC*/` markers and has no dependency on the page, so it is easy to test on its own.

## Limits

- Temperatures are in °F and wind is in mph.
- Wind chill only applies at 50°F and below. Wind has no effect on warmer days.
- This is a clothing guide, not medical advice. In extreme cold or heat, shorten the run or move indoors.

## Credits

Weather data by [Open-Meteo](https://open-meteo.com/). Wind chill and heat index use the standard National Weather Service formulas.
