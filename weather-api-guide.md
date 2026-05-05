# Weather Dashboard - API Implementation Guide

## Overview
The Weather Dashboard is a comprehensive web application that fetches and displays real-time weather data using the **OpenWeatherMap API**.

## Features

### 1. **Current Weather Display**
- Real-time temperature and conditions
- Feels-like temperature
- High/Low temperatures
- Humidity and pressure levels
- Wind speed and direction
- Visibility information
- Cloud coverage percentage

### 2. **Weather Forecasting**
- 5-day weather forecast with daily summaries
- Hourly forecast for the next 48 hours
- Temperature trends
- Precipitation probability

### 3. **Air Quality Information**
- Air Quality Index (AQI) levels
- PM 2.5 and PM 10 measurements
- Nitrogen dioxide (NO₂) levels
- Ozone (O₃) concentrations
- Sulfur dioxide (SO₂) levels

### 4. **Sun Information**
- Sunrise time
- Sunset time
- Day length calculation

### 5. **Location Features**
- Search by city name
- Geolocation support (GPS)
- Multiple location searches
- Current location detection

## API Integration Details

### Endpoints Used

#### 1. Current Weather Endpoint
```
https://api.openweathermap.org/data/2.5/weather?q={city}&units={units}&appid={API_KEY}
```

**Parameters:**
- `q`: City name (e.g., "Betla", "Ranchi")
- `units`: metric (Celsius) or imperial (Fahrenheit)
- `appid`: Your OpenWeatherMap API key

**Response Includes:**
- Temperature and feels-like temperature
- Weather condition description
- Wind data
- Humidity and pressure
- Visibility
- Cloud coverage
- Sunrise/Sunset times

#### 2. Forecast Endpoint
```
https://api.openweathermap.org/data/2.5/forecast?lat={lat}&lon={lon}&units={units}&appid={API_KEY}
```

**Features:**
- 5-day forecast with 3-hour intervals
- 40 forecast records (5 days × 8 intervals)
- Hourly temperature and weather conditions

#### 3. Air Pollution Endpoint
```
https://api.openweathermap.org/data/2.5/air_pollution?lat={lat}&lon={lon}&appid={API_KEY}
```

**Data Provided:**
- AQI values (1-5 scale)
- Individual pollutant levels
- Health recommendations

### Coordinate-based Request
```javascript
// Get weather by latitude and longitude
https://api.openweathermap.org/data/2.5/weather?lat={latitude}&lon={longitude}&units={units}&appid={API_KEY}
```

## Setup Instructions

### 1. Get OpenWeatherMap API Key
1. Visit: https://openweathermap.org/api
2. Sign up for a free account
3. Get your free API key from the dashboard
4. Free tier includes:
   - Current weather data
   - 5-day forecast
   - Air quality index
   - 1,000 calls/day limit

### 2. Update API Key in Code
Replace the API key in `weather-dashboard.html`:

```javascript
const API_KEY = 'your_api_key_here';
```

### 3. Enable CORS (if needed)
The OpenWeatherMap API supports CORS, so direct calls from browsers work fine.

## Usage

### Search by City
```javascript
function searchWeather() {
    const city = cityInput.value.trim();
    if (city) {
        fetchWeatherData(city);
    }
}
```

### Get Current Location
```javascript
function getCurrentLocation() {
    navigator.geolocation.getCurrentPosition(
        (position) => {
            const { latitude, longitude } = position.coords;
            fetchWeatherByCoords(latitude, longitude);
        }
    );
}
```

### Fetch Weather Data
```javascript
function fetchWeatherData(city) {
    const url = `${API_BASE}/weather?q=${city}&units=${currentUnits}&appid=${API_KEY}`;
    
    fetch(url)
        .then(response => response.json())
        .then(data => {
            displayWeather(data);
            fetchForecastData(data.coord.lat, data.coord.lon);
            fetchAirQuality(data.coord.lat, data.coord.lon);
        })
        .catch(error => console.error('Error:', error));
}
```

## Temperature Units

### Metric (Default)
- Temperature: Celsius (°C)
- Wind: m/s
- Visibility: km
- Pressure: mb (millibars)

### Imperial
- Temperature: Fahrenheit (°F)
- Wind: mph
- Visibility: miles
- Pressure: mb

## Error Handling

### Common Errors and Solutions

1. **401 Unauthorized**
   - Issue: Invalid API key
   - Solution: Verify your API key is correct

2. **404 Not Found**
   - Issue: City name not found
   - Solution: Check spelling or use alternative city name

3. **429 Too Many Requests**
   - Issue: API rate limit exceeded (1,000 calls/day for free tier)
   - Solution: Implement caching or upgrade plan

4. **Network Error**
   - Issue: No internet connection
   - Solution: Check internet connection

## Caching Strategy

To avoid excessive API calls:

```javascript
// Store weather data with timestamp
let cachedWeather = {};
let lastUpdate = 0;
const CACHE_DURATION = 3600000; // 1 hour in milliseconds

if (Date.now() - lastUpdate < CACHE_DURATION) {
    // Use cached data
    displayWeather(cachedWeather);
} else {
    // Fetch new data
    fetchWeatherData(city);
}
```

## Data Refresh

- Weather data: Every 1 hour
- Forecast data: Every 3-6 hours
- Air quality: Every 1 hour
- Hourly updates available with paid API tier

## API Response Example

```json
{
  "coord": {"lon": 84.049, "lat": 23.873},
  "weather": [
    {
      "id": 800,
      "main": "Clear",
      "description": "clear sky",
      "icon": "01d"
    }
  ],
  "main": {
    "temp": 28.5,
    "feels_like": 30.2,
    "temp_min": 25.1,
    "temp_max": 32.3,
    "pressure": 1010,
    "humidity": 65
  },
  "wind": {
    "speed": 3.5,
    "deg": 220
  },
  "clouds": {"all": 10},
  "visibility": 10000,
  "sys": {
    "country": "IN",
    "sunrise": 1620000000,
    "sunset": 1620045600
  },
  "name": "Betla"
}
```

## Advanced Features

### 1. Geolocation
The dashboard includes HTML5 Geolocation API integration:
```javascript
navigator.geolocation.getCurrentPosition(successCallback, errorCallback);
```

### 2. Real-time Updates
Fetch updated weather every hour automatically

### 3. Multiple Locations
Compare weather between different cities

### 4. Weather Icons
Unicode emoji icons for visual representation:
- ☀️ Clear
- ☁️ Cloudy
- 🌧️ Rainy
- ⛈️ Thunderstorm
- ❄️ Snow

## API Pricing

**Free Tier (Current Implementation)**
- 1,000 calls/day
- Current weather API
- Forecast API
- Air Pollution API
- No historical data

**Paid Tiers**
- Professional: 100,000 calls/day - $10/month
- Enterprise: Unlimited calls - Contact sales

## Performance Optimization

1. **Lazy Loading**: Load air quality and forecast after initial weather display
2. **Caching**: Cache data for 1 hour
3. **Throttling**: Limit search requests to 1 per second
4. **Async Operations**: Use async/await for cleaner code
5. **Image Optimization**: Use minimal icon files

## Browser Compatibility

- Chrome 40+
- Firefox 35+
- Safari 10+
- Edge 12+
- Mobile browsers (iOS Safari, Chrome Mobile)

## Testing

### Test Cities
- Betla (Default)
- Ranchi
- Delhi
- Mumbai
- Bangalore
- London
- New York

### Test Locations
- Use browser's geolocation feature
- Test with different coordinates

## Future Enhancements

1. ✅ Multi-language support
2. ✅ Dark mode
3. ✅ Weather alerts and notifications
4. ✅ Historical weather data
5. ✅ Custom map integration
6. ✅ Weather comparison tool
7. ✅ Extended forecast (14 days)
8. ✅ Offline support (Progressive Web App)

## Support and Resources

- **API Documentation**: https://openweathermap.org/api
- **Weather Codes**: https://openweathermap.org/weather-conditions
- **FAQ**: https://openweathermap.org/faq
- **Support**: https://openweathermap.org/support

## License

This weather dashboard uses the OpenWeatherMap API under their terms of service.
Free tier: Attribution required
Paid tier: Commercial usage allowed

---

**Last Updated**: May 5, 2026
**Version**: 1.0
**Status**: Production Ready
