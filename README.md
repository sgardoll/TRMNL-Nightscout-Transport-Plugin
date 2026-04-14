# Health & Transport Dashboard for TRMNL

A dashboard plugin for TRMNL that shows real-time glucose monitoring, daily vital metrics, your next calendar events, live public transport departures, and current weather — all in a clean two-column layout tuned for the TRMNL e-ink display.

[![TRMNL YouTube Tutorial](https://img.youtube.com/vi/MPm60wxAQKY/0.jpg)](https://www.youtube.com/watch?v=MPm60wxAQKY)

### Watch: [The Calm Tech Revolution: Building a custom dashboard with TRMNL | Tutorial](https://www.youtube.com/watch?v=MPm60wxAQKY)

## Features

### 🩺 Glucose Monitoring
- **Real-time readings** with a trend-direction pill badge
- **Historical line chart** rendered from Nightscout glucose history (falls back to a simulated trend line if history is unavailable)
- **Configurable alert thresholds** — high/low limits set in plugin settings drive an inverted alert card state
- **Medical safety warnings** prominently displayed

### 💪 Vital Metrics
- **Today's step count** from Google Fit
- **Last night's sleep duration** as a formatted label (e.g. `7h 15m`)
- Clean two-tile layout with minimal iconography

### 📅 Upcoming Schedule
- **Next two events** from Google Calendar, rendered side by side
- **All-day event detection** — date-only events render as `All day` instead of a bogus midnight time range
- Title whitespace is trimmed automatically

### 🌤 Weather & Forecast
- **Current temperature** with a condition icon (clear, partly cloudy, cloudy, rain, snow, storm)
- **Forecast sentence** composed from feels-like temperature, humidity, and wind direction
- **Wind direction** rendered as a British-English adjective (e.g. `north-westerly`) in the forecast sentence
- Icon set maps Google Weather API's 41 condition enums down to six display categories

### 🚌 Transit Departures
- **Next four live departures** with route badge, destination, and minutes away
- **Black header bar** with transit icon for visual contrast
- **Imminent departures** (≤1 minute) render as `Now`
- **Delay badge** (`+Nm`) renders when the source feed reports one

### 📊 Visual Design
- **Two-column layout** — narrow left column (time, vitals, glucose) + wide right column (schedule, transit, weather)
- **Clean light cards** with thin 1px borders, tuned for e-ink legibility
- **Inverted weather tile** (black background, white text) for visual weight
- **2-bit friendly palette** — black, white, and two grays

## Setup Requirements

### Prerequisites

1. A **TRMNL device** with plugin support
2. A **Buildship workflow** that fetches and reformats the required data into the webhook payload (remix template below)
3. Accounts and credentials for the data sources you want to render:
   - **Nightscout** instance and API token (for glucose)
   - **Google Weather API** key via Google Cloud (for weather)
   - **Google Fit** OAuth 2.0 refresh token (for steps and sleep)
   - **Google Calendar** OAuth connection (for upcoming events)
   - **Your transit provider** API key (the remix template ships with Transport for NSW; swap for your own provider as needed)
4. Internet connection for live data updates

### Quick Start: Remix the Buildship workflow

Get a preconfigured workflow with every data source node and the reformat node already wired up:

**https://buildship.vip/remix/d2ce3208-6c71-4c88-b491-9c5410490a93**

After remixing:
- Add your credentials for each data source as Buildship secrets / OAuth integrations
- Configure the lat/long inputs for the Weather and Transit nodes to your location
- Publish the workflow to get your webhook URL
- Paste that URL into the plugin's **Data Webhook URL** setting in TRMNL

### Data Webhook Requirements

Your webhook must return JSON in this shape. Only the fields the template actually reads are listed; extra fields are ignored.

```json
{
  "merge_variables": {
    "timestamp": "2026-04-15T05:30:00.000Z",
    "glucose": {
      "value": "5.4",
      "unit": "mmol/L",
      "mgdl": 97,
      "trend": "flat",
      "timestamp": "2026-04-15T05:28:00.000Z",
      "delta": "+0",
      "delta_mgdl": 0,
      "history": [
        { "time": "2026-04-15T05:13:00.000Z", "value": 5.3, "mgdl": 95 },
        { "time": "2026-04-15T05:18:00.000Z", "value": 5.3, "mgdl": 95 },
        { "time": "2026-04-15T05:23:00.000Z", "value": 5.4, "mgdl": 97 },
        { "time": "2026-04-15T05:28:00.000Z", "value": 5.4, "mgdl": 97 }
      ]
    },
    "health": {
      "steps": 2662,
      "sleep": {
        "duration_min": 435,
        "duration_label": "7h 15m",
        "start": "2026-04-14T22:00:00.000Z",
        "end": "2026-04-15T05:15:00.000Z"
      }
    },
    "schedule": [
      {
        "title": "Design Review",
        "start": "2026-04-15T11:00:00+10:00",
        "end": "2026-04-15T12:30:00+10:00"
      },
      {
        "title": "Team Offsite",
        "start": "2026-04-16",
        "end": "2026-04-17"
      }
    ],
    "transport": {
      "location": "Bayswater Rd before New Beach Rd",
      "departures": [
        {
          "route": "324",
          "destination": "City Center",
          "mins_away": 4,
          "departure_time": "2026-04-15T05:34:00Z"
        }
      ]
    },
    "weather": {
      "temperature": {
        "current": 15,
        "feels_like": 14,
        "unit": "celsius"
      },
      "condition": {
        "description": "Partly Cloudy",
        "type": "partly_cloudy"
      },
      "humidity": 85,
      "wind": {
        "direction_label": "north-westerly"
      }
    }
  }
}
```

**Schedule events** can be either timed (`start` is a full ISO datetime containing a `T`) or all-day (`start` is a date-only string like `"2026-04-16"`). The template detects each case and renders timed events as a time range (`11:00 AM – 12:30 PM`) and all-day events as the literal text `All day`.

## Installation

1. **Remix the Buildship workflow** (link above), add your credentials, and publish to get your webhook URL
2. **Install the plugin** on your TRMNL device
3. **Configure the plugin settings** in the TRMNL UI (see below)
4. **Verify** by hitting your webhook in a browser — you should see the JSON payload above — and confirm the TRMNL displays the dashboard correctly on its next refresh

## Configuration Options

### Required Settings

| Setting | Description | Example |
|---|---|---|
| Data Webhook URL | Your Buildship webhook endpoint | `https://xxxxx.buildship.run/trmnlPluginWebhook` |
| Transport Stop Name | Display name for your transport location | `Central Station` |

### Optional Settings

| Setting | Default | Description |
|---|---|---|
| High Glucose Alert | `10.0` mmol/L | At or above this value, the glucose card inverts to alert state (black background, white text) |
| Low Glucose Alert | `4.0` mmol/L | At or below this value, the glucose card inverts to alert state |
| Refresh Interval | 5 minutes | How often TRMNL polls the webhook |

> **Note:** Glucose alert thresholds are now evaluated in the Liquid template by comparing the current reading against these plugin settings. The Buildship workflow does **not** need to return a `glucose.status` field any more.

## Glucose Trend Directions

The plugin recognises the standard Nightscout trend strings and a few synonyms. Each maps to a symbol in the trend pill:

| Direction | Symbol | Meaning |
|---|---|---|
| `DoubleUp` | `↑↑` | Rising fast |
| `SingleUp` | `↗` | Rising |
| `FortyFiveUp` | `↑` | Rising slowly |
| `Flat` | `→` | Stable |
| `FortyFiveDown` | `↓` | Falling slowly |
| `SingleDown` | `↘` | Falling |
| `DoubleDown` | `↓↓` | Falling fast |

## Medical Disclaimer

⚠️ **IMPORTANT MEDICAL WARNING** ⚠️

This plugin is for **informational purposes only** and should **NEVER** be used for medical decisions. The displayed glucose data may be outdated by several minutes or more.

**Always:**
- Follow advice from medical specialists
- Use proper medical devices for treatment decisions
- Contact emergency services (112/911 or your local equivalent) if you feel unwell

## Technical Details

### Dependencies
- **Highcharts 12.3.0** — loaded from TRMNL's CDN for the glucose history chart (with an inline SVG fallback if Highcharts fails to load)
- **TRMNL Liquid engine** — template rendering

### Chart Features
- **Real historical data** from `glucose.history[]` when the Buildship workflow provides it
- **Graceful fallback** to a 6-point simulated trend line extrapolated from the current value and trend direction when history is unavailable
- **Compact sparkline** — axis-less, inside the glucose card
- **Auto-scaling Y-axis** with `softMin`/`softMax` tuned for mmol/L ranges

### Alert Logic
The template coerces `glucose.value` to a number and compares it against the `glucose_alert_high` and `glucose_alert_low` plugin settings. When the current reading is at or above the high threshold or at or below the low threshold, the glucose card switches to an inverted (black/white) alert state.

### Template Variables Used
- `merge_variables.glucose.{value, trend, timestamp, history}`
- `merge_variables.health.{steps, sleep.duration_label}`
- `merge_variables.schedule[].{title, start, end}`
- `merge_variables.transport.{location, departures[]}`
- `merge_variables.weather.{temperature, condition, humidity, wind.direction_label}`
- `glucose_alert_high`, `glucose_alert_low` (from plugin settings)
- `trmnl.user.utc_offset` (from the TRMNL runtime)

### Browser Compatibility
- Modern browsers with ES6+ support
- Highcharts runtime requirements
- TRMNL display engine

## Development

### File Structure
```
├── full.liquid     # Main template file
├── settings.yml    # Plugin configuration
├── plugin.json     # Plugin metadata
└── README.md       # This documentation
```

## Troubleshooting

### Common Issues

**No data displaying:**
- Check that the webhook URL is correct and reachable
- Verify the webhook returns JSON in the expected format
- Check TRMNL device network connectivity

**Glucose chart not showing:**
- Ensure Highcharts is loading (check browser console when previewing)
- Verify the payload includes either `glucose.history[]` or at least `glucose.value` + `glucose.trend`

**Steps and Sleep showing `0` / `—`:**
- The Google Fit node isn't wired into the reformat node's `health` input in Buildship
- The user genuinely has no activity for today / no sleep session logged last night
- The Google Fit OAuth refresh token has expired and needs rotating

**Schedule shows "No upcoming events":**
- The Google Calendar node isn't wired into the reformat node's `schedule` input
- The Calendar API call is missing `timeMin=<now>` — without it, the API returns the earliest events in the calendar's history instead of upcoming ones
- There genuinely are no events in the next window

**Forecast sentence missing the wind clause:**
- The weather payload doesn't include `wind.direction_label`
- The current cardinal direction isn't in the 16-point compass map in the Buildship reformat node

**Transport data missing:**
- Confirm `transport.departures` is a non-empty array
- Check that departure times are valid ISO datetime strings
- Verify route and destination strings are populated

## Support

For issues and feature requests, please open an issue on the project repository.

## Attribution

Contains a fork of [Nightscout Glucose Trend](https://usetrmnl.com/recipes/30521).

## License

This project is open source and available under the MIT License.

---

*Built for the TRMNL community with ❤️*
