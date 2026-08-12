# Bayut API Postman Collection

A ready-to-use Postman collection for the [Bayut Property Data API](https://rapidapi.com/happyendpoint/api/uae-real-estate3/) - covers all endpoints with pre-filled example requests, environment variables, and test scripts.

Built by [Happy Endpoint](https://happyendpoint.com) - API docs at [bayutapi.dev](https://bayutapi.dev).

---

## Import the collection

### Option 1 - Import from URL

In Postman, click **Import** and paste this URL:

```
https://raw.githubusercontent.com/happyendpointhq/bayut-api-postman-collection/main/bayut-api.postman_collection.json
```

### Option 2 - Download and import

Download `bayut-api.postman_collection.json` from this repo and drag it into Postman.

---

## Setup

### 1. Create an environment

In Postman, create a new environment called **Bayut API** with these variables:

| Variable | Value |
|---|---|
| `rapidapi_key` | Your RapidAPI key |
| `base_url` | `https://uae-real-estate3.p.rapidapi.com` |
| `location_id` | `5003` (Dubai Marina - change as needed) |

Get your API key by subscribing here: https://rapidapi.com/happyendpoint/api/uae-real-estate3/

### 2. Select the environment

Make sure the **Bayut API** environment is selected in the top-right dropdown before running requests.

---

## Collection structure

```
Bayut API
├── Health
│   └── GET /health
├── Location
│   └── GET /autocomplete
├── Property Search
│   ├── GET /search-property (for sale)
│   ├── GET /search-property (for rent)
│   ├── GET /search-property (with filters)
│   ├── GET /search-new-projects
│   └── GET /property-details
├── Agents
│   ├── GET /agent-search
│   ├── GET /agent-search-by-name
│   ├── GET /agent-details
│   └── GET /agent-properties
├── Agencies
│   ├── GET /agency-search
│   ├── GET /agency-search-by-name
│   ├── GET /agency-details
│   ├── GET /agency-agents
│   └── GET /agency-properties
├── Developers
│   └── GET /developer-search-by-name
└── Market Data
    ├── GET /transactions
    └── GET /amenities-search
```

---

## Endpoint reference

### Health check

```
GET {{base_url}}/health
```

Headers:
```
x-rapidapi-host: uae-real-estate3.p.rapidapi.com
x-rapidapi-key: {{rapidapi_key}}
```

Response:
```json
{ "success": true }
```

---

### Location autocomplete

```
GET {{base_url}}/autocomplete?query=dubai+marina&langs=en
```

Use this first to get `externalID` values for other endpoints.

Example response:
```json
{
  "success": true,
  "data": {
    "locations": [
      {
        "id": 36,
        "externalID": "5003",
        "name": { "en": "Dubai Marina" },
        "slug": { "en": "/dubai/dubai-marina" },
        "level": 2,
        "type": "neighborhood",
        "path": "Dubai > Dubai Marina",
        "adCount": 12450
      }
    ],
    "total": 1
  }
}
```

---

### Search properties for sale

```
GET {{base_url}}/search-property
  ?purpose=for-sale
  &location_ids={{location_id}}
  &property_type=apartments
  &rooms=1,2
  &price_min=500000
  &price_max=2000000
  &sort_order=popular
  &page=1
  &langs=en
```

**purpose** options: `for-sale`, `for-rent`

**property_type** options:
- Residential: `apartments`, `villas`, `townhouses`, `penthouse`, `hotel-apartments`, `residential-plots`
- Commercial: `offices`, `warehouses`, `shops`, `showrooms`, `commercial-villas`
- Or use `residential` / `commercial` to get all sub-types

**sort_order** options: `popular`, `latest`, `verified`, `trubroker_first`, `lowest_price`, `highest_price`

**rooms**: comma-separated, `0` = studio. Example: `0,1,2,3`

Example response (abbreviated):
```json
{
  "success": true,
  "data": {
    "properties": [
      {
        "externalID": "10283980",
        "title": { "en": "2BR Apartment in Dubai Marina" },
        "price": 1500000,
        "rooms": 2,
        "baths": 3,
        "area": 120.5,
        "purpose": "for-sale",
        "completionStatus": "completed",
        "furnishingStatus": "furnished",
        "isVerified": true,
        "referenceNumber": "REF-12345",
        "coverPhoto": { "url": "https://..." },
        "location": [
          { "name": { "en": "UAE" }, "level": 0 },
          { "name": { "en": "Dubai" }, "level": 1 },
          { "name": { "en": "Dubai Marina" }, "level": 2 }
        ]
      }
    ],
    "total": 1250,
    "page": 1,
    "totalPages": 53,
    "hitsPerPage": 24
  }
}
```

---

### Search properties for rent

```
GET {{base_url}}/search-property
  ?purpose=for-rent
  &location_ids={{location_id}}
  &property_type=apartments
  &rooms=1
  &price_max=80000
  &rent_frequency=yearly
  &is_furnished=furnished
  &page=1
```

**rent_frequency** options: `yearly`, `monthly`, `weekly`, `daily`

**is_furnished** options: `furnished`, `unfurnished`

---

### Search with amenity filter

First, get available amenity names:

```
GET {{base_url}}/amenities-search?query=pool
```

Response:
```json
{
  "success": true,
  "data": {
    "amenities": [
      { "value": "Swimming Pool", "count": 45230 },
      { "value": "Rooftop Pool", "count": 3100 }
    ]
  }
}
```

Then use the `value` field in your property search:

```
GET {{base_url}}/search-property
  ?purpose=for-sale
  &location_ids=5003
  &amenities=Swimming Pool,Gym,Parking
```

---

### Search off-plan projects

```
GET {{base_url}}/search-new-projects
  ?location_ids={{location_id}}
  &property_type=residential
  &completion_percentage=0-25
  &pre_handover_payment=50
  &sort_order=latest
  &page=1
```

**completion_percentage** options: `any`, `0-25`, `25-50`, `50-75`, `75-100`

**pre_handover_payment**: integer 0-100, filters projects where the pre-handover payment is at most this percentage

---

### Get property details

```
GET {{base_url}}/property-details?external_id=13495633&langs=en
```

Use `externalID` from search results. Returns full property data including all photos, floor plans, amenities, agent contact, and geographic coordinates.

---

### Search agents by location

```
GET {{base_url}}/agent-search
  ?location_ids={{location_id}}
  &purpose=for-sale
  &category=residential
  &page=1
  &langs=en
```

**category** options: `residential`, `commercial`

---

### Search agents by name

```
GET {{base_url}}/agent-search-by-name?query=john&page=1&langs=en
```

---

### Get agent details

```
GET {{base_url}}/agent-details?agent_id=2518657&langs=en
```

Use `externalID` from agent search results.

---

### Get agent's listings

```
GET {{base_url}}/agent-properties?owner_id=2243594&page=1&langs=en
```

Note: use `ownerID` from agent-details response, not `externalID`. These are different fields.

---

### Search agencies by location

```
GET {{base_url}}/agency-search?location_id=1&page=1&langs=en
```

Use `1` for all of Dubai, `3` for all of Abu Dhabi.

---

### Search agencies by name

```
GET {{base_url}}/agency-search-by-name?query=emaar&page=1&langs=en
```

---

### Get agency details

```
GET {{base_url}}/agency-details?agency_id=8566
```

---

### Get agents in an agency

```
GET {{base_url}}/agency-agents?agency_id=10212&page=1
```

---

### Get agency's listings

```
GET {{base_url}}/agency-properties?agency_external_id=106317&page=1&langs=en
```

Use `externalID` from agency search results.

---

### Search developers

```
GET {{base_url}}/developer-search-by-name?query=emaar&page=1&langs=en
```

Returns developer IDs you can use in `/search-new-projects` as `developer_ids`.

---

### Transaction history

```
GET {{base_url}}/transactions
  ?purpose=for-sale
  &location_ids={{location_id}}
  &category_ids=residential
  &time_period=12m
  &completion_status=any
  &sort=date_desc
  &page=1
```

**time_period** options: `1m`, `3m`, `6m`, `12m`, `24m`

**sort** options: `date_desc`, `date_asc`, `price_desc`, `price_asc`, `area_desc`, `area_asc`

---

## Postman test scripts

The collection includes pre-written test scripts for each request. Here are a few examples you can add to the **Tests** tab in Postman:

### Basic response validation

```javascript
pm.test("Status is 200", function () {
    pm.response.to.have.status(200);
});

pm.test("Response is JSON", function () {
    pm.response.to.be.json;
});

pm.test("Success is true", function () {
    const body = pm.response.json();
    pm.expect(body.success).to.be.true;
});
```

### Save location ID to environment

Add this to the autocomplete request Tests tab to automatically save the first result's ID:

```javascript
pm.test("Has locations", function () {
    const body = pm.response.json();
    pm.expect(body.data.locations.length).to.be.above(0);

    // Save first location ID for use in other requests
    const firstId = body.data.locations[0].externalID;
    pm.environment.set("location_id", firstId);
    console.log("Saved location_id:", firstId);
});
```

### Validate property search response

```javascript
pm.test("Has properties array", function () {
    const body = pm.response.json();
    pm.expect(body.data).to.have.property("properties");
    pm.expect(body.data.properties).to.be.an("array");
});

pm.test("Has pagination info", function () {
    const body = pm.response.json();
    pm.expect(body.data).to.have.property("total");
    pm.expect(body.data).to.have.property("page");
    pm.expect(body.data).to.have.property("totalPages");
});

pm.test("Properties have required fields", function () {
    const body = pm.response.json();
    const props = body.data.properties;

    if (props.length > 0) {
        const first = props[0];
        pm.expect(first).to.have.property("externalID");
        pm.expect(first).to.have.property("price");
        pm.expect(first).to.have.property("purpose");
    }
});
```

---

## Common location IDs

| Area | externalID |
|---|---|
| Dubai (whole emirate) | 1 |
| Abu Dhabi (whole emirate) | 3 |
| Dubai Marina | 5003 |
| Downtown Dubai | 6901 |
| Palm Jumeirah | 5002 |
| Business Bay | 5460 |
| JVC | 6388 |
| JLT | 5006 |
| Dubai Hills Estate | 11621 |
| Jumeirah | 5001 |

---

## Error responses

All errors follow this format:

```json
{
  "success": false,
  "message": "Invalid request: Missing required parameter",
  "error": "VALIDATION_ERROR"
}
```

| HTTP Status | Error Code | Meaning |
|---|---|---|
| 400 | VALIDATION_ERROR | Missing or invalid parameter |
| 404 | API_ERROR | Resource not found |
| 500 | INTERNAL_ERROR | Server error |
| 502 | API_ERROR | Upstream service unavailable |

---

## Links

- API on RapidAPI: https://rapidapi.com/happyendpoint/api/uae-real-estate3/
- Full documentation: https://bayutapi.dev
- Happy Endpoint: https://happyendpoint.com
- Twitter: https://x.com/happyendpointhq
- Email: happyendpointhq@gmail.com

Need bulk Bayut data (100K+ records)? Email happyendpointhq@gmail.com


---

## Using this API from Claude, Cursor, or another MCP client

RapidAPI hosts an MCP server, so you can query this API from an AI assistant
without writing any code:

```json
{
  "mcpServers": {
    "Bayut UAE Real Estate": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://mcp.rapidapi.com",
        "--header",
        "x-api-host: uae-real-estate3.p.rapidapi.com",
        "--header",
        "x-api-key: YOUR_RAPIDAPI_KEY"
      ]
    }
  }
}
```

| Client | Config path |
|---|---|
| Claude Desktop (macOS) | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| Claude Desktop (Windows) | `%APPDATA%\Claude\claude_desktop_config.json` |
| Cursor | `~/.cursor/mcp.json` |
| Claude Code | `.mcp.json` in your project root |

---

## Related repos

- [bayut-api](https://github.com/happyendpointhq/bayut-api) - full endpoint documentation
- [bayut-api-python-examples](https://github.com/happyendpointhq/bayut-api-python-examples) - Python client and nine runnable examples
- [bayut-api-javascript-nextjs](https://github.com/happyendpointhq/bayut-api-javascript-nextjs) - working Next.js 15 app
- [uae-real-estate-data-guide](https://github.com/happyendpointhq/uae-real-estate-data-guide) - every route to UAE property data

---

## Disclaimer

Happy Endpoint is an independent provider. This project is **not affiliated
with, endorsed by, sponsored by, or connected to** any of the websites,
platforms, retailers, or marketplaces referenced here or reachable through the
underlying APIs.

All product names, brands, trademarks, and registered trademarks are the
property of their respective owners. Any reference to them is descriptive only,
to identify the subject matter of the data, and does not imply any association
or endorsement.

Users are responsible for ensuring their use of any data complies with
applicable laws and the terms of service of the relevant source.

---

## About Happy Endpoint

[Happy Endpoint](https://happyendpoint.com) builds and maintains real-time data
APIs for property portals, retailers, and marketplaces. All APIs are available on
RapidAPI with a free tier.

- Catalogue: [happyendpoint.com/library](https://happyendpoint.com/library)
- Datasets: [happyendpoint.com/datasets](https://happyendpoint.com/datasets)
- Documentation: [docs.happyendpoint.com](https://docs.happyendpoint.com)
- Contact: happyendpointhq@gmail.com

## Licence

MIT. See [LICENSE](LICENSE).
