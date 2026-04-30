# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **Strapi 5.34.0** headless CMS application configured as a blog template. It provides a backend API for managing blog content with a built-in admin interface. The template includes example data (categories, authors, articles, about page, and global settings) that can be imported on first run.

**Tech Stack:**
- **Framework:** Strapi 5.34.0
- **Database:** SQLite (default), MySQL, or PostgreSQL
- **Plugins:** users-permissions, cloud, upload
- **Runtime:** Node.js 18-22
- **UI Customization:** React 18, styled-components

## Key Architecture

### Content Types
The application defines five main content types in `src/api/`:
- **article** - Blog posts with slug, cover image, blocks (rich content), authors, and categories
- **author** - Author profiles with avatar
- **category** - Blog post categories
- **global** - Site-wide settings (favicon, SEO defaults, metadata)
- **about** - About page with blocks (rich content sections)

Content types use dynamic zones/blocks for flexible content composition. The `shared.media` and `shared.slider` blocks handle file attachments.

### Configuration Structure
- `config/database.js` - Database connection config (SQLite/MySQL/PostgreSQL with env overrides)
- `config/server.js` - Server settings
- `config/admin.js` - Admin panel config
- `config/api.js` - API configuration
- `config/middlewares.js` - Request middlewares
- `config/plugins.js` - Plugin initialization
- `src/bootstrap.js` - App initialization and seed data logic

### Seed Data & File Uploads
- `scripts/seed.js` - Runnable seed script (compiled Strapi instance)
- `src/bootstrap.js` - Exports the seed logic; called on app startup
- `data/data.json` - Seed data definitions
- `data/uploads/` - Image assets referenced by seed data
- First-run check uses Strapi's plugin store to prevent re-import
- Automatically uploads files and creates all content entries on first run
- Sets public permissions to allow reading articles, categories, authors, global, and about data

## Environment Setup

**Required:** Create a `.env.local` file in the project root with:
```env
ADMIN_JWT_SECRET=your-secret-key
API_TOKEN_SALT=your-salt
TRANSFER_TOKEN_SALT=your-salt
```

These secrets are required for the admin panel authentication and API token generation. A template `.env.local` is provided in the repository.

## Common Commands

### Development
```bash
npm run develop      # Start with auto-reload (development mode)
npm run start        # Start without auto-reload (production mode)
npm run build        # Build admin panel
```

### Database & Seed
```bash
npm run seed:example # Import seed data manually (can run anytime)
```

### Deployment
```bash
npm run deploy       # Deploy to Strapi Cloud
```

## File Structure

```
src/
  api/               # Content type definitions
    about/           # About page content type
    article/         # Articles content type
    author/          # Authors content type
    category/        # Categories content type
    global/          # Global site settings content type
  admin/             # Admin panel customizations
  components/
    shared/          # Shared React components (blocks, etc.)
  bootstrap.js       # App initialization and seed logic
  index.js           # Strapi entry point

config/
  database.js        # Database configuration
  server.js          # Server config (host, port, admin URL)
  admin.js           # Admin panel config
  api.js             # API config (versioning, prefix)
  middlewares.js     # Express middlewares
  plugins.js         # Plugin registration

data/
  data.json          # Seed data (categories, authors, articles, etc.)
  uploads/           # Image assets for seed data

database/
  migrations/        # Database migrations (currently empty)

types/
  generated/         # Auto-generated TypeScript types from content types

scripts/
  seed.js            # Standalone seed script (can be run independently)
```

## Database

**Default Database:** SQLite (`.tmp/data.db`)

**Supported Databases:**
- SQLite: Default, local development
- MySQL: Set `DATABASE_CLIENT=mysql`
- PostgreSQL: Set `DATABASE_CLIENT=postgres`

**Configuration:** Use environment variables to override defaults:
```
DATABASE_CLIENT=sqlite|mysql|postgres
DATABASE_HOST, DATABASE_PORT, DATABASE_NAME, DATABASE_USERNAME, DATABASE_PASSWORD
DATABASE_SSL=true/false (for MySQL/PostgreSQL)
DATABASE_FILENAME (for SQLite)
```

## Bootstrap & Initialization

`src/bootstrap.js` runs automatically when the Strapi app starts and:
1. Checks if seed data has been imported (using Strapi's plugin store)
2. If first run: imports categories, authors, articles, about page, and global settings
3. Uploads all referenced images from `data/uploads/`
4. Sets public role permissions to allow read-only access to blog content
5. Prevents re-import on subsequent restarts

To manually re-run seed: `npm run seed:example` (resets the first-run flag and re-imports all data).

## Content & Blocks

Articles and About pages use dynamic zones/blocks:
- **shared.media** - Single image with caption
- **shared.slider** - Multiple images carousel
- Custom blocks can be added in `src/components/shared/`

File upload to Strapi is handled during seed to replace file names with actual file objects.

## Admin & Permissions

The app uses the **users-permissions** plugin:
- Bootstrap automatically sets the public role to allow reading articles, categories, authors, global, and about
- Restrict write permissions to authenticated users in the admin panel
- Custom role and permission management is done through the Strapi admin UI

## Extending the Template

**Adding a New Content Type:**
1. Create a new folder in `src/api/your-type/`
2. Add content type schema files (Strapi scaffolds these)
3. Update `src/bootstrap.js` to import seed data if needed
4. Add example data to `data/data.json`
5. Run `npm run develop` to apply changes

**Customizing the Admin Panel:**
- Add React components and customizations in `src/admin/`
- Strapi rebuilds the admin panel automatically

## Deployment

**Strapi Cloud:** Run `npm run deploy` to push to Strapi Cloud if configured.

**Environment Setup:** Create a `.env.local` file with environment-specific values (database credentials, API URLs, etc.). This file is not committed to version control.
