# Health & Transport Dashboard for TRMNL

A dashboard plugin for TRMNL that displays glucose monitoring data, weather information, and public transport departures with a beautiful glucose trend background chart.

[![TRMNL YouTube Tutorial](https://img.youtube.com/vi/MPm60wxAQKY/0.jpg)](https://www.youtube.com/watch?v=MPm60wxAQKY)

## Features

### 🩺 Glucose Monitoring
- **Real-time glucose readings** with trend arrows
- **Background trend chart** showing glucose direction
- **Alert highlighting** for high/low glucose levels
- **Medical safety warnings** prominently displayed
- **Text outline effects** for readability over chart background

### 🌤️ Weather Display
- Current temperature and "feels like" temperature
- Weather condition descriptions with emoji icons
- Humidity percentage display
- Automatic weather icon selection

### 🚌 Transport Information
- **Live departure times** for public transport
- **Multiple routes** with destinations
- **Imminent departure alerts** (highlighted in red)
- **Delay information** when available
- **Service status notifications** (optional)

### 📊 Visual Design
- **Two-column responsive layout**
- **Clean card-based interface**
- **Glucose trend background chart** using Chartkick + Highcharts
- **Alternating departure row styling** for easy reading
- **Alert color coding** for urgent information

## Setup Requirements

### Prerequisiteswhy w
1. **TRMNL device** with plugin support
2. **Data webhook** that provides the required data format
3. **Internet connection** for live data updates

### Data Webhook Requirements

Your webhook must return JSON data in this exact format:

**Quick Start:** You can remix this BuildShip workflow template: https://buildship.vip/remix/59aca8c8-748c-4ed4-8caf-37e0665f5175

```json
{
  "merge_variables": {
    "timestamp": "2025-09-05T11:33:12.580Z",
    "glucose": {
      "value": "8.2",
      "unit": "mmol/L",
      "trend": "flat",
      "timestamp": "2025-09-05T11:32:05.389Z",
      "status": "normal"
    },
    "datetime": "2025-09-05T11:32:05.389Z",
    "sgv": "8.2",
    "direction": "Flat",
    "transport": {
      "location": "Your Stop Name",
      "departures": [
        {
          "route": "324",
          "destination": "City Center",
          "platform": "A1",
          "mins_away": 6,
          "departure_time": "2025-09-05T11:39:06Z"
        }
      ],
      "service_status": "Good service" // Optional
    },
    "weather": {
      "temperature": {
        "current": 15.4,
        "feels_like": 15,
        "unit": "celsius"
      },
      "condition": {
        "description": "Cloudy",
        "type": "cloudy",
        "icon_url": "https://maps.gstatic.com/weather/v1/cloudy"
      },
      "humidity": 61,
      "uv_index": 0,
      "wind": {
        "speed": 13,
        "direction": "southeast",
        "gust": 17,
        "unit": "kilometers_per_hour"
      }
    }
  }
}
```

## Installation

1. **Install the Plugin**
   - Add this plugin to your TRMNL device
   - Configure the required settings (see below)

2. **Configure Settings**
   - **Data Webhook URL**: Your webhook endpoint that provides the data
   - **Transport Stop Name**: Display name for your transport location
   - **Glucose Alert Levels**: High/low thresholds for glucose alerts (in mmol/L)

3. **Test Your Setup**
   - Verify your webhook returns the correct data format
   - Check that your TRMNL displays the dashboard correctly

## Configuration Options

### Required Settings
| Setting | Description | Example |
|---------|-------------|---------|
| Data Webhook URL | Your webhook endpoint | `https://your-domain.com/webhook` |
| Transport Stop Name | Display name for transport location | `Central Station` |

### Optional Settings
| Setting | Default | Description |
|---------|---------|-------------|
| High Glucose Alert | 10.0 mmol/L | Glucose level to highlight in red |
| Low Glucose Alert | 4.0 mmol/L | Glucose level to highlight in red |
| Refresh Interval | 5 minutes | How often to fetch new data |

## Glucose Directions

The plugin supports these glucose trend directions:

| Direction | Symbol | Meaning |
|-----------|--------|---------|
| `DoubleUp` | ⇈ | Rising fast |
| `SingleUp` | ↗ | Rising |
| `FortyFiveUp` | ↑ | Rising slowly |
| `Flat` | → | Stable |
| `FortyFiveDown` | ↓ | Falling slowly |
| `SingleDown` | ↘ | Falling |
| `DoubleDown` | ⇊ | Falling fast |

## Medical Disclaimer

⚠️ **IMPORTANT MEDICAL WARNING** ⚠️

This plugin is for **informational purposes only** and should **NEVER** be used for medical decisions. The displayed glucose data may be outdated by an hour or more. 

**Always:**
- Follow advice from medical specialists
- Use proper medical devices for treatment decisions
- Contact emergency services (112/911) if you feel unwell

## Technical Details

### Dependencies
- **Chartkick 5.0.1** - Chart rendering library
- **Highcharts 12.3.0** - Chart visualization engine
- **TRMNL Liquid Engine** - Template processing

### Chart Features
- **Background positioning** - Chart appears behind glucose data
- **Semi-transparent styling** - Doesn't interfere with text readability
- **Text outline effects** - White shadows ensure text visibility
- **Trend simulation** - Creates 3-point trend line based on current direction
- **Responsive sizing** - Adapts to card container dimensions

### Browser Compatibility
- Modern browsers with ES6+ support
- Highcharts compatibility requirements
- TRMNL display engine support

## Development

### File Structure
```
├── full.liquid         # Main template file
├── settings.yml       # Plugin configuration
└── README.md          # Documentation
```

### Template Variables Used
- `merge_variables.sgv` - Glucose value
- `merge_variables.direction` - Glucose trend direction
- `merge_variables.glucose.*` - Nested glucose data
- `merge_variables.weather.*` - Weather information
- `merge_variables.transport.*` - Transport data

### Customization
The template uses CSS custom properties for easy theming:
```css
:root {
  --color-accent: #ff0000;        /* Alert color */
  --color-accent-light: #ffe6e6;  /* Alert background */
  --font-size-large: 4rem;        /* Time display */
  --font-size-medium: 2.5rem;     /* Main values */
}
```

## Troubleshooting

### Common Issues

**No data displaying:**
- Check webhook URL is correct and accessible
- Verify webhook returns correct JSON format
- Check TRMNL network connectivity

**Glucose chart not showing:**
- Ensure Chartkick/Highcharts libraries are loading
- Check browser console for JavaScript errors
- Verify glucose data (`sgv`, `direction`, `datetime`) is present

**Transport data missing:**
- Confirm `transport.departures` array is populated
- Check departure times are in correct ISO format
- Verify route/destination strings are present

**Styling issues:**
- Check CSS is loading correctly
- Verify responsive grid layout
- Test on different screen sizes

## Support

For issues and feature requests, please create an issue on the project repository.

## Attribution

Contains a fork of 'Nightscout Glucose Trend' (https://usetrmnl.com/recipes/30521)

## License

This project is open source and available under the MIT License.

---

*Built for the TRMNL community with ❤️*
