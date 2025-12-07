# Web Dashboard - Rideau Canal Sensor Data

## 1. Overview

### Dashboard Features
- Real-time monitoring of three Rideau Canal locations (Dows Lake, Fifth Avenue, NAC)
- Live display of ice thickness, surface temperature, and snow accumulation
- Safety status indicators for each location
- Historical data visualization with interactive charts
- Auto-refresh every 30 seconds
- Responsive web interface

### Technologies Used
- **Node.js** - Backend runtime environment
- **Express.js** - Web server framework
- **Azure Cosmos DB** - NoSQL database for sensor data storage
- **@azure/cosmos** - Azure Cosmos DB SDK
- **Chart.js** - Data visualization library
- **HTML/CSS/JavaScript** - Frontend interface
- **CORS** - Cross-origin resource sharing
- **dotenv** - Environment variable management

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

3. **Start the application**
   
   Development mode (with auto-restart):
   ```bash
   npm run dev
   ```
   
   Production mode:
   ```bash
   npm start
   ```

4. **Access the dashboard**
   
   Open browser: `http://localhost:3000`

## 4. Configuration

1. **Create environment file**
   ```bash
   cp .env.example .env
   ```

2. **Edit `.env` with your credentials:**
   ```env
   COSMOS_ENDPOINT=https://your-cosmos-db-account.documents.azure.com:443/
   COSMOS_KEY=your_cosmos_db_primary_key
   COSMOS_DATABASE=RideauCanalDB
   COSMOS_CONTAINER=SensorAggregations
   PORT=3000
   ```

## 5. API Endpoints

### GET /api/latest
Retrieves the most recent sensor readings from all three locations.

**Request:**
```bash
GET http://localhost:3000/api/latest
```

**Response:**
```json
{
  "success": true,
  "timestamp": "2024-01-15T10:05:00Z",
  "data": [
    {
      "id": "Dows-Lake_2024-01-15T10:05:00Z",
      "location": "Dows-Lake",
      "windowEndTime": "2024-01-15T10:05:00Z",
      "AvgIceThickness": 25.4,
      "AvgSurfaceTemperature": -8.2,
      "MaxSnowAccumulation": 3.5,
      "SafetyStatus": "Safe"
    }
  ]
}
```

### GET /api/history/:location
Retrieves historical data for a specific location.

**Request:**
```bash
GET http://localhost:3000/api/history/Dows-Lake?limit=12
```

**Response:**
```json
{
  "success": true,
  "location": "Dows-Lake",
  "data": [
    {
      "id": "Dows-Lake_2024-01-15T09:00:00Z",
      "location": "Dows-Lake",
      "windowEndTime": "2024-01-15T09:00:00Z",
      "AvgIceThickness": 24.8,
      "AvgSurfaceTemperature": -7.5,
      "MaxSnowAccumulation": 3.2,
      "SafetyStatus": "Safe"
    }
  ]
}
```

### GET /api/status
Retrieves overall system status across all locations.

**Request:**
```bash
GET http://localhost:3000/api/status
```

**Response:**
```json
{
  "success": true,
  "overallStatus": "Safe",
  "locations": [
    {
      "location": "Dows-Lake",
      "safetyStatus": "Safe",
      "windowEndTime": "2024-01-15T10:05:00Z"
    }
  ]
}
```

### GET /api/all
Retrieves all data from the database (debugging purposes).

**Request:**
```bash
GET http://localhost:3000/api/all
```

**Response:**
```json
{
  "success": true,
  "count": 150,
  "data": [...]
}
```

### GET /health
Health check endpoint to verify server status.

**Request:**
```bash
GET http://localhost:3000/health
```

**Response:**
```json
{
  "status": "healthy",
  "timestamp": "2024-01-15T10:05:00Z",
  "cosmosdb": {
    "endpoint": "configured",
    "database": "RideauCanalDB",
    "container": "SensorAggregations"
  }
}
```

## 6. Deployment to Azure App Service

### Step-by-Step Deployment Guide

1. **Create Azure App Service**
   ```bash
   az webapp create \
     --resource-group <resource-group-name> \
     --plan <app-service-plan-name> \
     --name <app-name> \
     --runtime "NODE|18-lts"
   ```

2. **Configure Application Settings**
   ```bash
   az webapp config appsettings set \
     --resource-group <resource-group-name> \
     --name <app-name> \
     --settings \
       COSMOS_ENDPOINT="<your-cosmos-endpoint>" \
       COSMOS_KEY="<your-cosmos-key>" \
       COSMOS_DATABASE="RideauCanalDB" \
       COSMOS_CONTAINER="SensorAggregations" \
       PORT="8080"
   ```

3. **Deploy Application**
   
   Option A - ZIP Deployment:
   ```bash
   # Create deployment package
   zip -r deploy.zip . -x "*.git*" "node_modules/*"
   
   # Deploy to Azure
   az webapp deployment source config-zip \
     --resource-group <resource-group-name> \
     --name <app-name> \
     --src deploy.zip
   ```
   
   Option B - GitHub Actions:
   - Enable GitHub Actions deployment in Azure Portal
   - Push code to GitHub repository
   - Automatic deployment on push to main branch

4. **Verify Deployment**
   ```bash
   # Check application logs
   az webapp log tail --resource-group <resource-group-name> --name <app-name>
   
   # Test health endpoint
   curl https://<app-name>.azurewebsites.net/health
   ```

### Configuration Settings

**Required Environment Variables:**
- `COSMOS_ENDPOINT` - Azure Cosmos DB endpoint URL
- `COSMOS_KEY` - Azure Cosmos DB primary key
- `COSMOS_DATABASE` - Database name (RideauCanalDB)
- `COSMOS_CONTAINER` - Container name (SensorAggregations)
- `PORT` - Server port (8080 for Azure App Service)

**Azure App Service Settings:**
- Runtime: Node.js 18 LTS
- Always On: Enabled (for production)
- HTTPS Only: Enabled
- CORS: Configure allowed origins if needed

## 7. Dashboard Features

### Real-time Updates
- Automatic data refresh every 30 seconds
- Live timestamp display showing last update time
- Seamless updates without page reload
- Error handling with retry mechanism

### Charts and Visualizations
- **Ice Thickness Chart**: Line chart showing ice thickness trends over time for all three locations
- **Temperature Chart**: Line chart displaying surface temperature variations
- **Time-series Data**: Last 12 readings (1 hour of data at 5-minute intervals)
- **Color-coded Locations**: 
  - Dows Lake: Teal
  - Fifth Avenue: Red
  - NAC: Blue
- Interactive tooltips on hover
- Responsive chart sizing

### Safety Status Indicators
- **Location Cards**: Individual cards for each monitoring site displaying:
  - Ice thickness (cm)
  - Surface temperature (°C)
  - Snow accumulation (cm)
  - Safety status badge
- **Overall Status Badge**: Canal-wide safety status at the top of dashboard
- **Color-coded Status**:
  - Green: Safe
  - Yellow: Caution
  - Red: Unsafe
- Real-time status updates based on latest sensor data

## 8. Troubleshooting

### Common Issues and Fixes

**Connection Issues**
- Verify Cosmos DB credentials in `.env` file
- Check network connectivity to Azure
- Ensure Cosmos DB firewall allows your IP address
- Test connection: `curl http://localhost:3000/health`

**Port Conflicts**
- Change `PORT` value in `.env` file
- Check if port is in use: `netstat -ano | findstr :3000` (Windows) or `lsof -i :3000` (Mac/Linux)
- Kill process using the port or choose different port

**Missing Dependencies**
- Run `npm install` to install all required packages
- Delete `node_modules` and `package-lock.json`, then run `npm install` again
- Verify Node.js version: `node --version`

**No Data Displayed**
- Verify Cosmos DB contains data in `SensorAggregations` container
- Check browser console for API errors (F12 → Console tab)
- Test API endpoints directly: `http://localhost:3000/api/latest`
- Verify location names match: "Dows-Lake", "Fifth-Avenue", "NAC"

**Chart Not Rendering**
- Ensure Chart.js CDN is accessible (check network tab)
- Check browser console for JavaScript errors
- Verify historical data exists for all locations
- Clear browser cache and reload

**Environment Variables Not Loading**
- Ensure `.env` file is in project root directory
- Restart the server after modifying `.env`
- Check for typos in variable names (case-sensitive)
- Verify no spaces around `=` in `.env` file

**Azure Deployment Issues**
- Check application logs: `az webapp log tail`
- Verify all environment variables are set in Azure Portal
- Ensure PORT is set to 8080 for Azure App Service
- Check Application Insights for detailed error logs
