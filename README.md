# GA Dashboard

A real-time dashboard for monitoring SIM and Wallet management across managers and supervisors.

## Overview

The GA Dashboard provides comprehensive visibility into SIM card distribution and wallet balances across your organization. It features:

- **Manager & Supervisor Summaries**: Aggregated metrics displayed as interactive cards
- **Advanced Filtering**: Independent filters for POS, Manager, and Supervisor
- **Serialization Details**: Searchable table with per-column filtering
- **Auto-Refresh**: Keeps data current with configurable refresh intervals
- **Responsive Design**: Works seamlessly on desktop, tablet, and mobile devices

## Features

### Top Section: Summary Cards

**Manager Summary**
- Displays aggregated totals per manager
- Metrics: Total Wallet, Total SIM, GA-1, GA-0, SOH SIM
- Cards sorted by Total Wallet (descending)
- Hover effects and gradient backgrounds

**Supervisor Summary**
- Displays aggregated totals per supervisor
- Same metrics as Manager summary
- Helps identify supervisor-level performance

### Filters

**Top-Level Filters** (affect both summaries and table)
- **POS**: Filter by Point of Sale location
- **Manager**: Filter by manager name
- **Supervisor**: Filter by supervisor name
- Filters are independent (not cascading)
- "Clear All" button resets all filters

**Column Filters** (affect table only)
- Individual search boxes for each table column
- Debounced input (300ms delay) for performance
- Case-insensitive substring matching
- Multiple filters combine with AND logic

### Bottom Section: Serialization Table

- Displays up to 20 records at a time
- Vertical scrolling for navigation
- Sticky table header
- Color-coded GA badges:
  - **SOH**: Green (Stock on Hand)
  - **GA-1**: Purple
  - **GA-0**: Red

**Columns Displayed:**
- POS
- Serial Number
- GA (General Availability status)
- Manager
- Supervisor
- Region
- City

**Note**: The `updatedAt` column is excluded from display.

### Refresh Controls

- **Auto-Refresh**: Automatically updates data every 60 seconds
- **Manual Refresh**: Click the "Refresh" button to update immediately
- **Pause/Resume**: Toggle auto-refresh on/off as needed
- **Last Updated**: Displays timestamp of most recent data fetch

## Data Sources

The dashboard fetches data from two n8n webhooks:

1. **Combined Table**: `https://n8n.srv987649.hstgr.cloud/webhook/GACombinedTable`
   - Aggregated POS-level data
   - Used for manager and supervisor summaries

2. **Serialization Table**: `https://n8n.srv987649.hstgr.cloud/webhook/GASerialization`
   - Granular serial number records
   - Used for the detail table

Both endpoints should return JSON arrays. The dashboard handles empty responses and network errors gracefully.

## Installation & Deployment

### Local Development

1. Clone or download the repository
2. Open `index.html` directly in a web browser
3. No build process or dependencies required

### Web Server Deployment

Upload `index.html` to any web server or hosting platform:

**Static Hosting Options:**
- Vercel: `vercel deploy`
- Netlify: Drag and drop `index.html`
- GitHub Pages: Push to a repository and enable Pages
- Any Apache/Nginx server

**Configuration:**
All webhook URLs are configured in the JavaScript section at the top of `index.html`. Update these if your endpoints change:

```javascript
const CONFIG = {
  REFRESH_INTERVAL: 60 * 1000, // 1 minute
  COMBINED_TABLE_URL: 'https://n8n.srv987649.hstgr.cloud/webhook/GACombinedTable',
  SERIALIZATION_URL: 'https://n8n.srv987649.hstgr.cloud/webhook/GASerialization',
  MAX_TABLE_ROWS: 20,
};
```

## Usage Guide

### Filtering Data

**To filter by manager:**
1. Select a manager from the "Manager" dropdown
2. Summaries update to show only that manager's data
3. Table shows only records for that manager

**To filter by multiple criteria:**
1. Select values from multiple dropdowns (e.g., POS + Supervisor)
2. Both filters apply simultaneously (AND logic)
3. Use "Clear All" to reset

**To search the table:**
1. Type in any column filter input box
2. Table filters automatically (with 300ms debounce)
3. Multiple column filters combine with AND logic

### Refreshing Data

**Auto-Refresh:**
- Enabled by default
- Updates every 60 seconds
- Green pulsing dot indicates active refresh

**Manual Refresh:**
- Click the "Refresh" button anytime
- Button shows spinning animation during fetch

**Pause/Resume:**
- Click "Pause" to stop auto-refresh
- Button turns orange when paused
- Click "Resume" to restart

### Understanding the Metrics

- **Total Wallet**: Sum of all wallet balances
- **Total SIM**: Total number of SIM cards
- **GA-1**: General Availability level 1 count
- **GA-0**: General Availability level 0 count
- **SOH SIM**: Stock on Hand SIM cards

All metrics are aggregated from the POS-level data in the Combined Table webhook.

## Technical Details

### Architecture

- **Framework**: Vanilla JavaScript (no dependencies)
- **Styling**: Custom CSS with CSS variables
- **Build Process**: None required (single HTML file)
- **Browser Support**: Modern browsers (Chrome, Firefox, Safari, Edge)

### Design System

**Colors:**
- Background: `#0a0a0f` (dark theme)
- Accent: `#a855f7` (purple)
- Success: `#10b981` (green)
- Warning: `#f59e0b` (orange)
- Error: `#ef4444` (red)

**Typography:**
- Arabic text: Cairo font
- Numeric values: DM Sans font

### State Management

The dashboard maintains separate state for:
- Raw data from webhooks
- Filtered data (after top-level filters)
- Table display data (after column filters)
- Active filter selections
- Auto-refresh status

### Performance Optimizations

- Debounced column filter inputs (300ms)
- Maximum 20 table rows displayed
- Cache-busting on webhook requests
- Efficient data aggregation algorithms

## Troubleshooting

### Dashboard shows "No data available"

**Possible causes:**
1. Webhook endpoints are unreachable
2. Network connectivity issues
3. CORS restrictions on webhook responses
4. Webhook returning empty arrays

**Solutions:**
- Check browser console for error messages
- Verify webhook URLs in the CONFIG section
- Test webhook endpoints directly in browser
- Ensure webhooks return valid JSON arrays

### Filters not working

**Check:**
- Filter dropdowns populated with options?
- Console showing any JavaScript errors?
- Try clicking "Clear All" and re-applying filters

### Table not scrolling

**Verify:**
- Browser zoom level is normal (100%)
- Table has more than 20 rows of data
- CSS is loaded correctly (dark theme visible)

### Auto-refresh not working

**Check:**
- Pause button not in "Paused" state
- Console for fetch errors
- Network tab shows requests every 60 seconds

## Customization

### Change Refresh Interval

Edit the `REFRESH_INTERVAL` in CONFIG:

```javascript
const CONFIG = {
  REFRESH_INTERVAL: 30 * 1000, // 30 seconds instead of 60
  // ...
};
```

### Change Maximum Table Rows

Edit the `MAX_TABLE_ROWS` in CONFIG:

```javascript
const CONFIG = {
  MAX_TABLE_ROWS: 50, // Show 50 rows instead of 20
  // ...
};
```

### Update Color Theme

Modify CSS variables in the `:root` section:

```css
:root {
  --accent-primary: #3b82f6; /* Change to blue */
  /* ... */
}
```

### Add New Metrics

1. Ensure the data exists in the webhook response
2. Update the aggregation logic in `renderManagerSummaries()` and `renderSupervisorSummaries()`
3. Add new metric display in the card HTML

## Browser Compatibility

- **Chrome**: ✅ Fully supported
- **Firefox**: ✅ Fully supported
- **Safari**: ✅ Fully supported
- **Edge**: ✅ Fully supported
- **Internet Explorer**: ❌ Not supported

## Support

For issues, questions, or feature requests, please contact your system administrator or refer to the n8n workflow documentation.

## License

This dashboard is part of the internal n8n workflow system. Unauthorized distribution is prohibited.

## Version History

- **v1.0.0** (2026-02-06): Initial release
  - Manager and supervisor summaries
  - Independent filtering system
  - Serialization table with column filters
  - Auto-refresh with pause/resume
  - Responsive design
