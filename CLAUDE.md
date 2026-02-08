# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a single-file web application for the Village of Trumansburg that enables users to generate AI-powered summaries of YouTube videos from municipal meetings. The application is a static HTML page with embedded CSS and JavaScript that requires no build process or backend server.

## Architecture

**Single-Page Application (SPA)**: The entire application is contained in `index.html` with three main sections:
1. **Styles** (`<style>` block): All CSS styling using a Village of Trumansburg color scheme (blues and greens)
2. **HTML Structure**: Single-tab interface with video grid and modal workflow
3. **JavaScript Logic**: Client-side functionality for video loading and transcript handling

### Key Components

**Video Grid**:
- Displays paginated grid of videos from YouTube RSS feed
- Each video opens a 3-step modal workflow for AI summarization

**RSS Feed Loading**:
- Fetches videos from YouTube channel `UCpoU6jqTOqHo_bKkPg-vcTA`
- Implements multi-proxy CORS bypass strategy with retry logic
- Tries 5 different CORS proxy services in sequence: CORSProxy.io, AllOrigins, ThingProxy, RSS2JSON, Proxy CORS.sh
- Each proxy gets 2 retry attempts with exponential backoff (1s, 2s delays)
- Falls back to manual URL entry if all proxies fail

**Video Display**:
- Sorts videos by published date (newest first)
- Pagination: 4 videos per page (configurable via `videosPerPage` variable)
- Lazy-loaded thumbnails with fallback cascade (hqdefault → mqdefault → default)

**Manual Transcript & Summarization Workflow**:
1. User clicks a video from the grid, opening a 3-step modal
2. **Step 1**: User opens youtubetotranscript.com in new tab and copies transcript
3. **Step 2**: User pastes transcript into modal textarea
4. **Step 3**: User clicks button to copy formatted question and open Google Gemini
5. User pastes into Gemini chat and receives AI-generated summary

This manual workflow was chosen because:
- YouTube blocks automated transcript extraction from bots
- Free/zero-cost requirement eliminates API-based solutions
- Manual copy-paste is simple and reliable for low-traffic site

## Development Workflow

### Testing Locally
Open `index.html` directly in a web browser (no server required):
```bash
open index.html  # macOS
```

Or use Python's built-in HTTP server:
```bash
python3 -m http.server 8000
# Then visit http://localhost:8000
```

### Deploying the Site
This is a static site - deploy `index.html` to any static hosting service (GitHub Pages, Netlify, Vercel, etc.). No build step required.

### Making Changes

**Styling**: All styles are in the `<style>` block. The color palette uses:
- Primary: `#1e4a5f` (dark blue)
- Secondary: `#2d5b4a` (forest green)
- Accents: `#c8d6dc` (light blue-gray), `#f0f7f9` (very light blue)

**Video Loading**: Modify CORS proxy list in `loadVideos()` function (lines 440-469). Each proxy requires:
- `name`: Display name for logging
- `url`: Proxy endpoint with encoded RSS URL
- `parser`: Function to parse response (`parseXmlResponse` or `parseRss2JsonResponse`)

**Modal Workflow**:
- Modal opens when user clicks a video
- Uses youtubetotranscript.com for manual transcript copying
- Formats query for Google Gemini AI summarization
- All steps use clear, numbered instructions for non-technical users

**Pagination**: Adjust `videosPerPage` variable to change videos displayed per page.

## Important Notes

- **Pure static architecture**: Single HTML file with no backend, build tools, or external dependencies
- **No build tools**: No package.json, webpack, or bundler required
- **CORS limitations**: RSS feed loading depends on third-party CORS proxies which may have rate limits or downtime
- **YouTube channel**: Hardcoded to Village of Trumansburg's channel ID (`UCpoU6jqTOqHo_bKkPg-vcTA`)
- **Manual workflow**: Uses manual copy-paste for transcripts to avoid YouTube bot blocking and maintain zero cost
- **External services**: Relies on youtubetotranscript.com for transcript extraction and Google Gemini for AI summarization (both free, no API keys)
- **Browser compatibility**: Uses modern JavaScript (ES6+), no transpilation for older browsers
- **Non-technical friendly**: All instructions designed for non-technical village staff with clear, step-by-step guidance
