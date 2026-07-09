# Kiosk Building Navigation

Interactive floor-map kiosk for CCACC-DC. Visitors can browse rooms on a SVG map, view room details, book spaces, and get turn-by-turn path guidance between locations.

**Display target:** 3000 × 4500 portrait touchscreen.

---

## Features

- **Interactive floor map** — Click any room on the 1F / 2F SVG maps
- **Room info panel** — Photo, category, sq ft, rates, deposit, description, and booking link (bottom-left)
- **Path navigation** — Select start/end rooms, animated route line on the map, multi-floor support via elevator
- **Floor switcher** — 1F / 2F buttons on the right side
- **Kiosk-optimized UI** — Large touch targets and typography for a high-resolution display

---

## Project Files

| File | Purpose |
|------|---------|
| `navigation.html` | Main kiosk app (map, panels, navigation logic) |
| `data.js` | Room metadata (`roomsData` object) |
| `data.json` | JSON copy of room data (reference / backup) |
| `bitmap2.svg` | Source floor plan used to build the embedded SVG |
| `images/` | Room photos (`room_101.jpg`, etc.) |
| `navigation.txt` | Early HTML prototype (not used in production) |
| `.github/workflows/deploy.yml` | Auto-deploy to Bluehost on push to `main` |

---

## Quick Start

1. Open `navigation.html` in a browser (local or on the kiosk).
2. Ensure `data.js` and the `images/` folder are in the same directory.
3. Click a room on the map to open the info panel.
4. Tap **開啟導覽** (bottom-right) to open the navigation panel.

No build step required — static HTML, CSS, and JavaScript only.

---

## Using the Kiosk

### Room info panel (bottom-left)

When a room is selected:

- Room name, tag, photo, and description
- Square footage, deposit, hourly rates
- **Book** — Opens the CCACC booking page (rooms with a `link` in `data.js`)
- **Set as Start** — Sets the room as navigation origin
- **Navigate Here** — Sets destination and starts navigation

### Navigation panel (bottom-right)

1. Tap **🔍 開啟導覽** to open the panel above the button.
2. Choose **起點房間** (start) and **終點房間** (end).
3. Tap **開始導航** to draw the path.
4. Tap **清除路徑** to remove the route line.
5. Tap **✕ 關閉導覽** to close the panel.

Cross-floor routes automatically route through the elevator node.

### Floor picker (right side)

- **1F** / **2F** — Switch between floor maps
- Active floor is highlighted in blue

---

## Customizing UI Size & Position

All kiosk sizing is controlled by CSS variables at the top of `navigation.html` (`:root` block):

```css
:root {
    /* Panel widths */
    --info-panel-width: 1100px;
    --nav-panel-width: 1200px;

    /* Navigation widget position (bottom-right corner) */
    --nav-widget-bottom: 48px;   /* smaller = closer to bottom edge */
    --nav-widget-right: 48px;    /* smaller = closer to right edge */

    /* Typography */
    --kiosk-font-sm: 28px;
    --kiosk-font-md: 36px;
    --kiosk-font-lg: 48px;
    --kiosk-font-xl: 56px;
    --nav-font-md: 42px;
    --nav-font-lg: 56px;
    --nav-font-xl: 64px;

    /* Spacing */
    --kiosk-pad: 48px;
    --kiosk-radius: 28px;
}
```

Increase these values if text or buttons still feel small on the physical kiosk.

---

## Editing Room Data

Room content lives in `data.js` as the `roomsData` object. Each room key must match the room `id` on the SVG map.

Example entry:

```js
"Conference Room 101": {
  "name": "101 Meeting Room",
  "tag": "Conference Room",
  "image": "images/room_101.jpg",
  "sq_ft": 110,
  "hourly_rate": { "associate": 15, "general": 15 },
  "deposit": 0,
  "desc": "適合小型團隊會議或私人洽談，空間精巧且採光良好。",
  "link": "https://www.ccacc-dc.org/Asset_1_en.aspx?tid=161&aid=1",
  "link_text": "📅 立即預約此空間",
  "color": "#28a745"
}
```

| Field | Description |
|-------|-------------|
| `name` | Display name in the info panel |
| `tag` | Category label |
| `image` | Path to room photo |
| `sq_ft` | Square footage |
| `hourly_rate` | `{ general, associate }` hourly prices |
| `deposit` | Deposit amount |
| `desc` | Room description |
| `link` | Booking URL (omit to hide the book button) |
| `link_text` | Book button label |

---

## Navigation Path Line

The route is drawn as an animated blue polyline (`#nav-guide-line`):

- Stroke width: **10px**
- Color: `#007bff`
- Animated dashed flow effect via CSS `line-flow` keyframes

Pathfinding uses BFS over hallway/room nodes parsed from the SVG `room-node` and `edge-node` layers.

---

## Deployment

Pushing to the `main` branch triggers GitHub Actions to FTP-sync the project to:

```
public_html/navigation/
```

Required repository secrets:

- `FTP_SERVER`
- `FTP_USERNAME`
- `FTP_PASSWORD`

The kiosk should load `navigation.html` from the deployed URL.

---

## UI Layout Summary

```
┌─────────────────────────────────────────────┐
│                                             │
│              Floor Map (SVG)                │
│                                    [2F]     │
│                                    [1F]     │
│                                             │
│  ┌──────────────┐              ┌─────────┐  │
│  │  Info Panel  │              │ Nav     │  │
│  │  (room info) │              │ Panel   │  │
│  └──────────────┘              └─────────┘  │
│                                 [開啟導覽]   │
└─────────────────────────────────────────────┘
```

- **Info panel** — Fixed bottom-left
- **Nav widget** — Fixed bottom-right (toggle button + slide-up panel)
- **Floor picker** — Fixed right center

---

## Recent UI Updates

- Scaled panels and typography for 3000×4500 portrait kiosk
- Moved navigation from full-height right sidebar to bottom-right floating widget
- Unified book button styling with other info-panel action buttons
- Restored original 10px animated navigation path line
