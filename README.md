# 🛵 Hurrier Follow-up Dashboard

A real-time order tracking and courier management dashboard for Hurrier delivery operations. Monitor active deliveries, track courier performance, and manage order assignments with an intuitive web interface.

**📊 Live Dashboard:** https://mohabatef-png.github.io/hurrier-followup-dashboard/

---

## Features

✨ **Real-time Monitoring**
- Live order status tracking (left pickup, near pickup, picked up, near dropoff, accepted)
- Auto-refresh every 60 seconds
- Status-based filtering and statistics

📍 **Zone & Routing Management**
- Filter orders by delivery zones (Mohandsien, Alexandria, Delta, October, etc.)
- Zone-based performance metrics
- Vehicle type classification (Bike vs. Bicycle)

👤 **Courier Intelligence**
- Courier assignment and status tracking
- Vehicle type monitoring
- Phone contact information
- Rider status display

💰 **Payment & Order Management**
- Cash vs. online payment tracking
- Order deadline monitoring
- Overdue order alerts
- Vendor filtering and sorting

📤 **Export & Integration**
- Export filtered data to CSV
- Slack message generation for team updates
- Copy dashboard link for sharing
- Live countdown to next refresh

🎯 **Smart Filtering**
- Multi-field search (order #, courier, vendor, zone)
- Sort by deadline, vendor, zone, payment type
- Reset filters with one click
- Real-time result counts

---

## Getting Started

### 1. **Install Bookmarklet**

The dashboard uses browser bookmarklets to capture live data from the Hurrier platform and push it to GitHub.

**Steps:**
1. Open the dashboard: https://mohabatef-png.github.io/hurrier-followup-dashboard/
2. Scroll to the bottom to see all available bookmarklets for each zone
3. Drag each bookmarklet to your browser's bookmarks bar
4. Or right-click and "Bookmark This Link"

### 2. **Capture Live Data**

When browsing Hurrier:
1. Navigate to the zone/hub you want to monitor
2. Click the corresponding bookmarklet (e.g., "🏙️ Mohandsien")
3. An alert will say: "Ready! Scroll the page once..."
4. The bookmarklet will fetch all active orders
5. Another alert will confirm: "Done! XX orders pushed."
6. The dashboard will auto-refresh and display the new data

### 3. **View Dashboard**

The dashboard automatically updates every 60 seconds with the latest data.

---

## Zone Bookmarklets

Each bookmarklet queries a specific zone and pushes data to the dashboard:

| Bookmarklet | Coverage | Hub Points |
|-------------|----------|------------|
| 🏙️ **Mohandsien** | Cairo West | 8 hubs |
| 🌊 **Alexandria** | Coastal Region | 14 hubs |
| 🌾 **Delta** | Northern Egypt | 16 hubs |
| 🏜️ **October** | October City | 18 hubs |
| 🐫 **Haram/Mokattam** | Greater Cairo | 15 hubs |
| 🛵 **Maadi/Mokattam** | South Cairo | 16 hubs |
| ✈️ **Nasr City/Heliopolis** | East Cairo | 16 hubs |
| 🏗️ **New Cities** | New Administrative Capital | 16 hubs |
| ✅ **Cairo** | All Cairo zones | 20 hubs |

---

## Dashboard Views

### Status Overview
At the top, see counts for each delivery status:
- **All** - Total active orders
- **Left Pickup** - Order just left restaurant
- **Near Pickup** - Courier near restaurant
- **Picked Up** - Order in transit
- **Near Dropoff** - Courier at customer location
- **Accepted** - Order accepted by system

### Vehicle Distribution
Quick view of delivery vehicle types:
- 🏍️ **Bikes** (motorcycles) - Faster, urban deliveries
- 🚲 **Bicycles** - Eco-friendly, local routes

### Order Table
Detailed view with columns:
- **Order #** - Unique order identifier
- **Vendor** - Restaurant/merchant name (truncated)
- **Overdue** - Minutes until deadline (negative = late)
- **Courier** - Driver name and status
- **Status** - Current delivery status
- **Zone** - Delivery location
- **Type** - Delivery type/category
- **Payment** - Cash or card
- **Agent** - Assigned team member

---

## Filtering & Sorting

### Search
Quick search across:
- Order number
- Courier name
- Vendor name
- Delivery zone

### Filters
- **All Zones** - Filter by delivery location
- **All Vendors** - Filter by restaurant/merchant
- **All Types** - Filter by delivery type
- **Payment** - Show cash or card orders
- **Vehicle Type** - Show bike or bicycle deliveries

### Sorting
- **Most Overdue First** - Urgent orders at top
- **Deadline Soonest** - By ETA
- **Order # ↓** - By order number
- **By Zone** - Grouped by location
- **By Vendor** - Grouped by merchant

---

## Team Management

### Assign Couriers
Each order can be assigned to a team member:
- **Unassigned** - No assignment
- **Mohamed, Ahmed, Sara, Omar, Nour, Youssef** - Available agents

Select from the Agent dropdown to track responsibility.

### Status Indicators
- 🔴 **Red** - Overdue order (late delivery)
- 🟠 **Orange** - At risk (< 15 min to deadline)
- 🟢 **Green** - On time
- ⚪ **Gray** - No deadline set

---

## Export & Sharing

### CSV Export
Click **↓ CSV** to download filtered orders as spreadsheet:
- All currently visible orders (respects all filters)
- Columns: Order, Vendor, Deadline, Courier, Status, Zone, Type, Payment, Agent
- Ready for Excel or Google Sheets

### Slack Message
Click **Slack** to copy a formatted message:
- Total orders snapshot
- Count of overdue, at-risk, and unassigned orders
- Top 8 overdue orders with details
- Paste directly into Slack

### Share Link
Click **Copy** in footer to share dashboard link with team.

---

## Data Structure

Each order record contains:
```json
{
  "id": "ORD-12345",           // Order identifier
  "v": "Restaurant Name",      // Vendor name (max 50 chars)
  "dm": 5,                     // Minutes to deadline (null = no deadline)
  "c": "Courier Name",         // Courier/driver name
  "phone": "01234567890",      // Courier contact
  "vehicle": "Bike",           // Vehicle type
  "rs": "active",              // Rider status
  "s": "picked_up",            // Order status
  "z": "10001",                // Zone ID
  "vt": "restaurants",         // Vertical type (category)
  "cash": 1,                   // 1 for cash, 0 for card
  "ts": 1722000000000,         // Timestamp
  "agent": "Unassigned"        // Assigned agent
}
```

---

## Status Definitions

| Status | Meaning |
|--------|----------|
| `left_pickup` | Order departed restaurant |
| `near_pickup` | Courier within delivery range of restaurant |
| `picked_up` | Order collected, in transit to customer |
| `near_dropoff` | Courier arrived at customer location |
| `accepted` | Order confirmed by system |

---

## Technical Details

**Framework:** Vanilla JavaScript (No dependencies)
**Storage:** GitHub via data.json
**Update Frequency:** Auto-refresh every 60 seconds
**Hosting:** GitHub Pages
**Browser Support:** Chrome, Firefox, Safari, Edge (modern versions)

### How It Works
1. **Bookmarklet** intercepts XHR requests in Hurrier app
2. Captures auth token and makes API calls
3. Processes 10,000 max active orders with pagination
4. Enriches data with courier details
5. Base64 encodes and pushes to `data.json` on GitHub
6. Dashboard fetches and displays with live filtering
7. Auto-refreshes every 60 seconds

---

## Troubleshooting

### No orders appearing?
- Run the bookmarklet while viewing active orders in Hurrier
- Check browser console (F12) for errors
- Ensure you're logged into Hurrier
- Allow pop-ups/redirects from the domain

### Old data showing?
- Click **↻ Refresh** button in top bar
- Wait for auto-refresh countdown (60 seconds)
- Clear browser cache and reload

### Bookmarklet not working?
- Make sure you're on Hurrier's website
- Check that bookmarklet is complete (not truncated)
- Try re-adding the bookmarklet
- Check browser developer console (F12) for errors

---

## Performance

- **Dashboard Load:** < 1 second (local data)
- **Data Fetch:** 2-5 seconds (depends on order count)
- **Auto-refresh:** Every 60 seconds
- **CSV Export:** Instant
- **Supports:** 10,000+ orders

---

## License

MIT License - Free for personal and commercial use

---

## Support

For issues or feature requests, open an issue on GitHub:
https://github.com/mohabatef-png/hurrier-followup-dashboard/issues

---

**Built with ❤️ for Hurrier delivery operations**
