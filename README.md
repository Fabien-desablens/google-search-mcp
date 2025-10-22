# Google Search MCP Server

A Model Context Protocol (MCP) server that provides Google Search functionality with automatic API key rotation and intelligent quota management.

<a href="https://glama.ai/mcp/servers/@Fabien-desablens/google-search-mcp">
  <img width="380" height="200" src="https://glama.ai/mcp/servers/@Fabien-desablens/google-search-mcp/badge" alt="Google Search Server MCP server" />
</a>

## Features

- Official Google Custom Search API integration
- Automatic API key rotation for increased daily limits
- **Persistent quota tracking** across sessions and directories
- Multi-language and geolocation support
- Advanced search filters (date, file type, site-specific)
- SafeSearch content filtering
- **Global configuration** - works from anywhere
- Compatible with Claude Desktop and other MCP clients

## Installation

### Quick Setup (Recommended)

```bash
# Install the package globally
npm install -g @kyaniiii/google-search-mcp

# Run interactive setup
google-search-setup
```

This will:
- ✅ Configure your Google API keys interactively
- ✅ Set up global configuration file
- ✅ Automatically integrate with Claude Code
- ✅ Enable 100+ free searches per day per API key

### Advanced Installation

```bash
git clone https://github.com/Fabien-desablens/google-search-mcp.git
cd google-search-mcp
npm install
npm run build
npm run setup
```

## Uninstallation

### Complete Removal

```bash
# Remove the package
npm uninstall -g @kyaniiii/google-search-mcp

# Remove from Claude Code
claude mcp remove google-search

# Remove configuration file (if desired)
npm run clean
```

**Note:** Configuration file (`~/.google-search-mcp.json`) is **always preserved** during updates and uninstallation to protect your API keys. Use `npm run clean` only if you want to completely remove your configuration.

## Configuration

The setup tool creates a **global configuration file** at:
- **Windows:** `%USERPROFILE%\.google-search-mcp.json`
- **Linux/macOS:** `~/.google-search-mcp.json`

This file contains:
- ✅ Your API keys and search engine IDs
- ✅ **Persistent quota tracking** (survives restarts)
- ✅ Automatic daily reset at midnight UTC
- ✅ Cross-directory usage (works from anywhere)

### Example Configuration
```json
{
  "keys": [
    {
      "id": "key_1",
      "apiKey": "AIzaSy...",
      "searchEngineId": "abc123...",
      "dailyUsage": 45,
      "dailyLimit": 100,
      "lastReset": "2024-07-30",
      "isActive": true
    }
  ],
  "lastUpdated": "2024-07-30T15:30:00Z",
  "version": "1.0.0"
}
```

**⚠️ Important:** If you manually edit the configuration file (`~/.google-search-mcp.json`), you must restart Claude Desktop for the changes to take effect.

## Getting Google API Credentials

### 1. Create a Google Cloud Project

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project or select existing
3. Enable the "Custom Search API"

### 2. Generate API Key

1. Navigate to "APIs & Services" > "Credentials"
2. Click "Create Credentials" > "API Key"
3. Copy the generated key

### 3. Create Custom Search Engine

1. Visit [Google Programmable Search Engine](https://programmablesearchengine.google.com/)
2. Click "Get started" or "Add"
3. For "Sites to search", enter `*` to search the entire web
4. Copy the Search Engine ID

### 4. Scale with Multiple Keys

- Each Google Cloud project provides 100 free searches/day
- Create multiple projects for more quota (e.g., 3 projects = 300 searches/day)
- The server automatically rotates between available keys

## Usage with Claude Code

After running the setup, the server is automatically configured in Claude Code. You can immediately use it:

```
User: "Search for latest AI news from this week"
Claude: I'll search for the latest AI news using Google Search...
```

The server exposes a `google_search` tool with these parameters:

### Required Parameters
- `query` (string): Search query

### Optional Parameters
- `num` (number): Number of results (1-10, default: 5)
- `start` (number): Starting index for results
- `safe` (string): SafeSearch level ('off', 'active')
- `lr` (string): Language restriction (e.g., 'lang_en', 'lang_fr')
- `gl` (string): Geolocation (country code: 'us', 'fr', 'uk')
- `dateRestrict` (string): Time period ('d1', 'w1', 'm1', 'y1')
- `fileType` (string): File type filter ('pdf', 'doc', 'ppt')
- `siteSearch` (string): Search specific site
- `siteSearchFilter` (string): Include ('i') or exclude ('e') site
- `cr` (string): Country restriction ('countryUS', 'countryFR')
- `exactTerms` (string): Exact phrase to include
- `excludeTerms` (string): Terms to exclude
- `orTerms` (string): Alternative terms (OR search)
- `sort` (string): Sort by date ('date')
- `searchType` (string): Search type ('image' for image search)

## Examples

### Basic Search
```json
{
  "query": "artificial intelligence news"
}
```

### Advanced Search
```json
{
  "query": "machine learning",
  "num": 10,
  "lr": "lang_en",
  "gl": "us",
  "dateRestrict": "m1",
  "fileType": "pdf"
}
```

### Site-Specific Search
```json
{
  "query": "typescript tutorial",
  "siteSearch": "stackoverflow.com",
  "siteSearchFilter": "i"
}
```

## Quota Management

The server provides real-time quota information in each response:

```json
{
  "results": [...],
  "metadata": {
    "quotaStatus": {
      "totalUsed": 15,
      "totalLimit": 300,
      "keysStatus": [
        {
          "id": "key_1",
          "used": 15,
          "limit": 100,
          "remaining": 85,
          "active": true
        }
      ]
    }
  }
}
```

### Quota Features

- Automatic daily reset at midnight UTC
- Intelligent key rotation when limits are reached
- Disabled keys automatically reactivate after reset
- **Persistent tracking** across server restarts
- No charges - stops at free tier limits

## Error Handling

The server gracefully handles various error scenarios:

- **Quota Exceeded**: Automatically rotates to next available key
- **All Keys Exhausted**: Returns clear error message with quota status
- **Invalid API Key**: Disables the key and continues with others
- **Network Errors**: Returns detailed error information

## Development

### Prerequisites

- Node.js 18+
- TypeScript
- Google Cloud account

### Scripts

```bash
# Development mode with auto-reload
npm run dev

# Build for production
npm run build

# Start server
npm start

# Setup configuration
npm run setup
```

### Project Structure

```
google-search-mcp/
├── src/
│   ├── index.ts         # Entry point
│   ├── server.ts        # MCP server setup
│   ├── config.ts        # Configuration wrapper
│   ├── global-config.ts # Global config manager
│   └── tools/
│       └── search.ts    # Search implementation
├── build/               # Compiled JavaScript
├── setup.js             # Interactive setup tool
└── package.json
```

## Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

## License

MIT License - see LICENSE file for details

## Support

For issues, questions, or contributions, please visit:
https://github.com/Fabien-desablens/google-search-mcp