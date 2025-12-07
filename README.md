# Web Dashboard - Rideau Canal Sensor Data

## 1. Overview

### What the Dashboard Does
The Rideau Canal Monitoring Dashboard is a real-time web application that visualizes sensor data from three locations along the Rideau Canal (Dows Lake, Fifth Avenue, and NAC). It displays ice thickness, surface temperature, snow accumulation, and safety status to help monitor skating conditions.

### Technologies Used
- **Node.js** - Backend runtime environment
- **Express.js** - Web server framework
- **Azure Cosmos DB** - NoSQL database for sensor data storage
- **Azure Cosmos DB SDK** - Database client library
- **Chart.js** - Data visualization library
- **HTML/CSS/JavaScript** - Frontend interface

## 2. Prerequisites

- Node.js (version 14 or higher)
- npm (comes with Node.js)
- Azure Cosmos DB account with:
  - Database: `RideauCanalDB`
  - Container: `SensorAggregations`
- Modern web browser (Chrome, Firefox, Edge, Safari)

## 3. Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd 25F_CST8916_Final_Project_Web-Dashboard
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

## 4. Configuration

1. **Create environment file**
   ```bash
   cp .env.example .env
   ```

2. **Edit `.env` with your Azure Cosmos DB credentials:**
   ```env
   COSMOS_ENDPOINT=https://your-cosmos-db-account.documents.azure.com:443/
   COSMOS_KEY=your_cosmos_db_primary_key
   COSMOS_DATABASE=RideauCanalDB
   COSMOS_CONTAINER=SensorAggregations
   PORT=3000
   ```

## 5. Usage

### Development Mode
```bash
npm run dev
```
Runs with nodemon for auto-restart on file changes.

### Production Mode
```bash
npm start
```

### Access the Dashboard
Open your browser and navigate to: `http://localhost:3000`

### API Endpoints
- `GET /api/latest` - Latest readings for all locations
- `GET /api/history/:location` - Historical data for specific location
- `GET /api/status` - Overall system status
- `GET /api/all` - All data (debugging)
- `GET /health` - Health check

## 6. Code Structure

### Main Components

**Backend (server.js)**
- Express server setup with CORS and static file serving
- Cosmos DB client initialization
- API route handlers for data retrieval
- Error handling and graceful shutdown

**Frontend (public/app.js)**
- Dashboard initialization and auto-refresh
- Data fetching from API endpoints
- UI updates for location cards and status badges
- Chart.js integration for historical data visualization

**UI (public/index.html & styles.css)**
- Responsive dashboard layout
- Location cards for each monitoring site
- Real-time charts for ice thickness and temperature
- Safety status indicators

### Key Functions

**Backend Functions:**
- `GET /api/latest` - Queries Cosmos DB for most recent readings from all three locations
- `GET /api/history/:location` - Retrieves time-series data for charting (default: last 12 readings)
- `GET /api/status` - Aggregates safety status across all locations

**Frontend Functions:**
- `initDashboard()` - Initializes dashboard and sets up auto-refresh (30s interval)
- `updateDashboard()` - Fetches latest data and updates UI
- `updateLocationCards()` - Updates metric displays for each location
- `updateCharts()` - Renders historical data using Chart.js
- `updateOverallStatus()` - Updates canal-wide safety status badge

## 7. Sensor Data Format

### JSON Schema
```json
{
  "id": "string",
  "location": "string",
  "windowEndTime": "ISO 8601 timestamp",
  "AvgIceThickness": "number (cm)",
  "AvgSurfaceTemperature": "number (°C)",
  "MaxSnowAccumulation": "number (cm)",
  "SafetyStatus": "string (Safe|Caution|Unsafe)"
}
```

### Example Output
```json
{
  "id": "Dows-Lake_2024-01-15T10:05:00Z",
  "location": "Dows-Lake",
  "windowEndTime": "2024-01-15T10:05:00Z",
  "AvgIceThickness": 25.4,
  "AvgSurfaceTemperature": -8.2,
  "MaxSnowAccumulation": 3.5,
  "SafetyStatus": "Safe"
}
```

## 8. Troubleshooting

### Common Issues and Fixes

**Connection Issues**
- Verify Cosmos DB credentials in `.env` file
- Check network connectivity to Azure
- Ensure Cosmos DB firewall allows your IP address

**Port Conflicts**
- Change `PORT` value in `.env` file
- Check if port 3000 is already in use: `netstat -ano | findstr :3000`

**Missing Dependencies**
- Run `npm install` to install all required packages
- Delete `node_modules` and `package-lock.json`, then reinstall

**No Data Displayed**
- Verify Cosmos DB contains data in `SensorAggregations` container
- Check browser console for API errors (F12)
- Test API endpoints directly: `http://localhost:3000/api/latest`

**Chart Not Rendering**
- Ensure Chart.js CDN is accessible
- Check browser console for JavaScript errors
- Verify historical data exists for all locations

**Environment Variables Not Loading**
- Ensure `.env` file is in project root directory
- Restart the server after modifying `.env`
- Check for typos in variable names
