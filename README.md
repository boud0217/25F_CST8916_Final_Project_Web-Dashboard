# Web Dashboard - Rideau Canal Sensor Data

## Setup Instructions

### Prerequisites
- Node.js (version 14 or higher)
- npm (comes with Node.js)
- Azure Cosmos DB account

### Local Development Setup

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd 25F_CST8916_Final_Project_Web-Dashboard
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Environment Configuration**
   ```bash
   cp .env.example .env
   ```
   
   Edit the `.env` file with your Azure Cosmos DB credentials:
   ```
   COSMOS_ENDPOINT=https://your-cosmos-db-account.documents.azure.com:443/
   COSMOS_KEY=your_cosmos_db_primary_key
   COSMOS_DATABASE=RideauCanalDB
   COSMOS_CONTAINER=SensorAggregations
   PORT=3000
   ```

4. **Start the application**
   
   For development (with auto-restart):
   ```bash
   npm run dev
   ```
   
   For production:
   ```bash
   npm start
   ```

5. **Access the dashboard**
   Open your browser and navigate to `http://localhost:3000`

## Deployment Instructions

### Azure App Service Deployment

1. **Create Azure App Service**
   ```bash
   az webapp create --resource-group <resource-group> --plan <app-service-plan> --name <app-name> --runtime "NODE|18-lts"
   ```

2. **Configure environment variables**
   ```bash
   az webapp config appsettings set --resource-group <resource-group> --name <app-name> --settings \
     COSMOS_ENDPOINT="<your-cosmos-endpoint>" \
     COSMOS_KEY="<your-cosmos-key>" \
     COSMOS_DATABASE="RideauCanalDB" \
     COSMOS_CONTAINER="SensorAggregations" \
     PORT="8080"
   ```

3. **Deploy the application**
   ```bash
   az webapp deployment source config-zip --resource-group <resource-group> --name <app-name> --src <zip-file>
   ```

### Docker Deployment

1. **Create Dockerfile** (if not exists):
   ```dockerfile
   FROM node:18-alpine
   WORKDIR /app
   COPY package*.json ./
   RUN npm ci --only=production
   COPY . .
   EXPOSE 3000
   CMD ["npm", "start"]
   ```

2. **Build and run**
   ```bash
   docker build -t web-dashboard .
   docker run -p 3000:3000 --env-file .env web-dashboard
   ```

### Environment Variables

| Variable | Description | Required |
|----------|-------------|----------|
| `COSMOS_ENDPOINT` | Azure Cosmos DB endpoint URL | Yes |
| `COSMOS_KEY` | Azure Cosmos DB primary key | Yes |
| `COSMOS_DATABASE` | Database name | Yes |
| `COSMOS_CONTAINER` | Container name | Yes |
| `PORT` | Server port (default: 3000) | No |

## Project Structure

```
├── public/           # Static files
│   ├── app.js       # Frontend JavaScript
│   ├── index.html   # Main HTML file
│   └── styles.css   # Styling
├── server.js        # Express server
├── package.json     # Dependencies
└── .env.example     # Environment template
```

## Troubleshooting

- **Connection issues**: Verify Cosmos DB credentials and network access
- **Port conflicts**: Change PORT in .env file
- **Dependencies**: Run `npm install` if modules are missing