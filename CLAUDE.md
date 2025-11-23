# CLAUDE.md - Shaplat Codebase Guide

## Project Overview

**Shaplat** is a Turkish-language content discovery platform deployed as a static single-page application on GitHub Pages with Firebase backend integration.

- **Domain:** www.shaplat.com
- **Stack:** HTML5, CSS3, Vanilla JavaScript (ES6+), Firebase
- **Architecture:** Monolithic SPA - all code in single `index.html` file (118KB, ~3,200 lines)

## Repository Structure

```
/home/user/shaplat/
├── index.html     # Complete app (HTML + CSS + JS)
├── CNAME          # GitHub Pages domain config
└── README.md      # Project description
```

## Key Technical Details

### No Build System
- Pure static files - no package.json, no build step
- External dependencies via CDN (Firebase v10.7.1, Google Fonts)
- Deploy by pushing to GitHub

### Firebase Configuration
Hardcoded in index.html (~line 1589-1597). App gracefully degrades to localStorage-only mode if Firebase fails.

### Global State
All state managed in `window.shaplatState` object containing:
- `currentUser`, `auth`, `db` - Auth/database
- `contents`, `currentContent`, `contentHistory` - Content data
- `selectedCategory`, `selectedTags`, `sortBy`, `searchQuery` - Filters
- `isFirebaseConfigured` - Firebase availability flag

## Code Organization in index.html

| Section | Lines | Content |
|---------|-------|---------|
| CSS Styles | 7-1583 | All styling, themes, animations |
| HTML Body | 1584-1800 | UI structure, modals |
| JavaScript | 1584-3196 | Firebase init, state, logic, rendering |

### Key Line References
- Firebase config: `index.html:1589-1597`
- Global state: `index.html:1611-1645`
- Authentication: `index.html:1771-1936`
- Content rendering: `index.html:2535-2620`
- Admin panel: `index.html:2909-3138`

## Development Patterns

### Theme System
Three themes: `dark`, `light`, `cyberpunk`
- Stored in `localStorage['theme']`
- Uses CSS custom properties for all colors

### Authentication
- Email/password, Google OAuth, Anonymous mode
- Admin role hardcoded for `admin@shaplat.com`
- Session stored in sessionStorage (local) or Firestore (Firebase mode)

### Data Models

**Content Object:**
```javascript
{ id, title, description, url, image, category, tags[], rating, ratingCount, viewCount, favoriteCount }
```

**User Object:**
```javascript
{ id, email, name, role, anonymous, shaplatCount, favoriteCount }
```

### LocalStorage Keys
- `theme` - User theme preference
- `shaplatContents` - Cached content
- `currentUser` - Session data
- `userRatings` - Ratings (key: `${userId}_${contentId}`)
- `userFavorites` - Favorites array

## UI Conventions

### Keyboard Shortcuts
- `SPACE` - Random content (SHAPLAT!)
- `←/→` - Navigate history
- `F` - Toggle favorite
- `S` - Share
- `1-5` - Rate content
- `ESC` - Close menu

### Categories (16 total)
Web Siteleri, Video, Müzik, Oyunlar, Teknoloji, Eğitim, Sanat & Tasarım, Sosyal Medya, Araçlar, Eğlence, Bilim & Doğa, Spor, Yemek & Tarif, Seyahat, Haberler, Diğer

### CSS Classes
- `.sidebar`, `.main-content`, `.section` - Layout
- `.content-card`, `.trend-card`, `.stat-card` - Cards
- `.btn`, `.btn-primary`, `.btn-outline` - Buttons
- `.nav-item`, `.category-item`, `.tag` - Navigation

## Code Style

- **Language:** Turkish UI, English code
- **Naming:** camelCase
- **Indentation:** 4 spaces
- **Quotes:** Single in JS, double in HTML
- **Emojis:** Heavily used in UI strings

## Common Development Tasks

### Adding Features
1. Locate relevant section in index.html
2. Add to `window.shaplatState` if new state needed
3. Create window function for public API
4. Add UI elements and CSS styles
5. Test all three themes
6. Test mobile viewport (breakpoint: 768px)

### Modifying Admin Features
- Check role: `window.shaplatState.currentUser.role === 'admin'`
- Admin UI functions: `renderAdminContents()`, `renderAddContent()`
- Admin tabs: `showAdminTab()`

### Bulk Import Format
```
Title|Description|https://url.com|https://image.jpg|Category|tag1,tag2,tag3
```

## Testing

**No testing infrastructure currently exists.** Manual testing only.

For testing changes:
1. Open index.html directly in browser
2. Test authentication flows
3. Test across all themes
4. Test mobile responsive layout
5. Test keyboard shortcuts

## Deployment

Push to GitHub → GitHub Pages auto-deploys

No build step required.

## Important Notes

- Single file architecture - search carefully when editing
- Firebase API keys are exposed (normal for client-side)
- No input sanitization - be careful with XSS
- Use CSS custom properties for any color/theme values
- Always test mobile layout after UI changes
