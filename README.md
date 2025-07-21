# LinkedIn Job Scraping Automation with n8n

This project contains an automated workflow for scraping LinkedIn job postings using n8n and the `linkedin-jobs-scraper` package.

## Project Structure

- `scrape/` - Contains the LinkedIn scraper script using linkedin-jobs-scraper package
- `workflows/` - Contains the n8n workflow file for job fetching automation
- `output/` - Storage for scraped job data and debugging logs
- `docker-compose.yaml` - Docker configuration for running n8n
- `Dockerfile` - Custom Docker image configuration with required dependencies
- `n8n-data/` - Persistent storage for n8n data (created when running Docker)

## Getting Started

1. Install Dependencies
   - Docker and Docker Compose for running n8n (recommended)
   - Or Node.js and npm if running directly on your system

2. Setup Scraper
   ```bash
   cd scrape
   npm install
   ```

3. Start n8n
   Using Docker (recommended):
   ```bash
   # Build the custom Docker image
   docker-compose build

   # Start the container
   docker-compose up -d
   ```
   This will start n8n at http://localhost:5678
   - Username: admin
   - Password: yourpassword

   Or run n8n directly if installed on your system:
   ```bash
   n8n start
   ```

4. Import n8n Workflow
   - Open your n8n instance at http://localhost:5678
   - Go to Workflows → Import From File
   - Select `workflows/linkedin-job-fetch.json`
   - Configure the following:
     1. Update the Execute Command node's path to match your system
     2. Set up Google Sheets credentials (see Google Sheets Setup below)
     3. Update the Google Sheet ID in the Save to Google Sheet node

## Docker Configuration

The project includes a custom Docker setup:

### Dockerfile
- Based on official n8n image
- Includes additional dependencies:
  - linkedin-jobs-scraper
  - puppeteer

### docker-compose.yaml
- Uses the custom Dockerfile
- n8n running on port 5678
- Basic authentication enabled
- Timezone set to America/Edmonton
- Persistent data storage in `./n8n-data`

To change the default credentials, modify the following environment variables in `docker-compose.yaml`:
- `N8N_BASIC_AUTH_USER`
- `N8N_BASIC_AUTH_PASSWORD`

## Google Sheets Setup

1. Create a Google Sheet
   - Create a new sheet with these columns:
     - Date
     - Title
     - Company
     - Location
     - Description
     - Job URL
     - Applied

2. Set up Google Sheets API
   - Go to Google Cloud Console
   - Create a new project
   - Enable Google Sheets API
   - Create OAuth 2.0 credentials
   - Download the credentials JSON file

3. Configure n8n
   - In n8n, go to Settings → Credentials
   - Click on "Add Credential"
   - Select "Google Sheets"
   - Follow the OAuth2 setup process
   - Use the downloaded credentials

4. Update Workflow
   - Open the workflow
   - Double click the "Save to Google Sheet" node
   - Update the Sheet ID (from your Google Sheet's URL)
   - Verify the range matches your sheet name (default: Sheet1!A:G)

## Usage

1. Run the scraper independently:
   ```bash
   cd scrape
   node scrape.js
   ```

2. Or use the n8n workflow:
   - Activate the workflow in your n8n instance
   - The workflow will:
     - Run daily at 9:00 AM
     - Scrape LinkedIn jobs
     - Format the data
     - Save to your Google Sheet
   - Monitor the execution in n8n's execution log

## Output

The scraper will save job data to:
- `output/jobs_log.json` (when run independently)
- Your configured Google Sheet (when run via n8n)

## License

MIT

## Contributing

Feel free to submit issues and pull requests. 
