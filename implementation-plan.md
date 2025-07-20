# TV Show Tracking App - Implementation Plan

## Executive Summary

This document outlines a comprehensive implementation plan for a mobile-optimized TV show tracking web application. The recommended tech stack prioritizes free/open-source solutions while maintaining scalability and modern development practices.

## Final Tech Stack Decision

### Primary Stack: Supabase Full-Stack
- **Platform:** Supabase (all-in-one solution)
- **Frontend:** React with Vite + Ant Design
- **Backend:** Supabase Edge Functions (TypeScript/JavaScript)
- **Database:** Supabase PostgreSQL with auto-generated APIs
- **Authentication:** Google OAuth only (via Supabase Auth)
- **TV Show Data:** TVMaze API
- **UI Library:** Ant Design (antd) for React components

**Total Monthly Cost:** $0 for development and small-scale production

### Alternative: Railway Full-Stack (Backup Option)
- **Platform:** Railway (integrated deployment)
- **Frontend:** React with Vite + Ant Design
- **Backend:** Express.js/Node.js API
- **Database:** Railway PostgreSQL
- **Authentication:** Google OAuth implementation
- **TV Show Data:** TVMaze API
- **UI Library:** Ant Design (antd) for React components

**Total Monthly Cost:** $5-15/month after trial credit

## Single Platform Benefits

### Simplified Development & Deployment
- **Unified Dashboard:** Manage frontend, backend, database, and authentication from one interface
- **Integrated APIs:** Auto-generated REST/GraphQL APIs eliminate custom backend boilerplate
- **Seamless Authentication:** Built-in user management with social login providers
- **Single Configuration:** Environment variables and secrets managed in one place

### Cost Efficiency
- **Bundled Pricing:** All services included in platform subscription
- **No Service Sprawl:** Avoid multiple vendor billing and management overhead
- **Predictable Costs:** Clear tier-based pricing vs. per-service billing

### Developer Experience
- **Faster Development:** Less configuration and integration work required
- **Single Point of Support:** One vendor for all technical issues
- **Consistent Documentation:** Unified guides and examples for all services
- **Simplified CI/CD:** Git-based deployments handle entire application stack

## Research Findings

### TV Show Database API

#### TVMaze API (Selected)
**Pros:**
- Completely free with no API key required
- Excellent TV show focus with detailed episode guides
- Clean, fast API with JSON responses
- No commercial restrictions on free tier
- Simple REST API following HATEOAS principles
- Network/platform information included
- Episode air dates and scheduling data

**Considerations:**
- Rate limited to 20 calls per 10 seconds
- Less extensive metadata compared to larger databases
- TV shows only (no movie data)

**Free Tier:** Unlimited usage, 20 calls per 10 seconds
**Documentation:** https://www.tvmaze.com/api

### Hosting Platforms

#### Supabase (Selected)
**Free Tier:** 2 projects, 500MB database, 1GB storage, 5GB bandwidth, 50k MAUs
**Cost:** Free tier, Pro at $25/month
**Full-Stack Features:** 
- PostgreSQL database with auto-generated REST/GraphQL APIs
- Built-in authentication (Google OAuth integrated)
- Real-time subscriptions and file storage
- Edge Functions for serverless backend logic
- Frontend hosting for static sites
- All services integrated on single platform

#### Railway (Backup Option)
**Free Tier:** $5 one-time credit trial (no ongoing free tier)
**Cost:** Usage-based pricing, typically $5-15/month for small apps
**Full-Stack Features:** 
- Deploy frontend, backend, and database together
- Git-based deployments with automatic Docker builds
- PostgreSQL databases included
- Environment management and scaling
- Zero-config deployments


## Detailed Implementation Plan

### Phase 1: Project Setup & Foundation

#### 1.1 Frontend Setup
```bash
# Create React app with Vite
npm create vite@latest tv-show-tracker -- --template react
cd tv-show-tracker
npm install

# Install essential dependencies
npm install react-router-dom @supabase/supabase-js
npm install antd # Ant Design components
npm install @ant-design/icons # Ant Design icons
```

#### 1.2 Backend Setup (Supabase Edge Functions)
```bash
# Install Supabase CLI
npm install -g supabase

# Initialize Supabase project
supabase init

# Create Edge Functions for custom logic
supabase functions new search-shows
supabase functions new manage-user-shows
```

#### 1.3 Database Setup
- Create Supabase account and project
- Set up authentication providers (Google OAuth)
- Design initial database schema:
  - `users` table (handled by Supabase Auth)
  - `user_shows` table (user_id, show_id, status, rating, date_added)
  - `shows` table (show_id, title, network, status, cached_data)

### Phase 2: Core Functionality

#### 2.1 Authentication System
- Implement Google OAuth with Supabase Auth
- Create protected routes in React
- Set up user session management
- Design login/signup UI components

#### 2.2 TV Show Search & Data Integration
- Integrate TVMaze API for show search
- Implement show details fetching
- Create caching layer for frequently accessed shows
- Design search UI with mobile-first approach

#### 2.3 User Show Management
- Build watchlist functionality
- Implement "previously watched" library
- Create rating system (1-5 stars)
- Design show card components

### Phase 3: Enhanced Features

#### 3.1 Show Details & Scheduling
- Display network/platform information
- Show episode release schedules
- Implement show status tracking (ongoing, ended, etc.)
- Create detailed show view pages

#### 3.2 Mobile Optimization
- Responsive design with Tailwind CSS
- Touch-friendly UI elements
- Progressive Web App (PWA) features
- Offline functionality for cached data

### Phase 4: Deployment & Production

#### 4.1 Supabase Full-Stack Deployment (Recommended)
- Deploy React app to Supabase hosting
- Deploy Edge Functions for custom backend logic
- Configure production environment variables
- Set up custom domain (optional)
- Set up continuous deployment from Git

#### 4.2 Alternative: Railway Full-Stack Deployment
- Deploy entire application stack to Railway
- Configure production environment variables
- Set up custom domain and SSL certificates
- Configure auto-deployment from Git repository

#### 4.3 Production Configuration (All Platforms)
- Set up Row Level Security (RLS) policies
- Configure authentication providers (Google OAuth)
- Implement rate limiting and security headers
- Set up monitoring and logging
- Create database backup strategy
- Optimize queries and indexes

## Technical Architecture

### Frontend Architecture
```
src/
├── components/
│   ├── auth/
│   ├── shows/
│   ├── ui/
│   └── layout/
├── pages/
├── hooks/
├── services/
├── utils/
└── styles/
```

### Backend Architecture
```
api/
├── routes/
│   ├── auth.js
│   ├── shows.js
│   └── users.js
├── middleware/
├── services/
│   ├── tvmaze.js
│   ├── tmdb.js
│   └── supabase.js
└── utils/
```

### Database Schema
```sql
-- Users handled by Supabase Auth

-- User's show tracking
CREATE TABLE user_shows (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES auth.users(id),
  show_id INTEGER NOT NULL,
  status VARCHAR(20) CHECK (status IN ('watching', 'completed', 'plan_to_watch')),
  rating INTEGER CHECK (rating >= 1 AND rating <= 5),
  date_added TIMESTAMP DEFAULT NOW(),
  date_updated TIMESTAMP DEFAULT NOW()
);

-- Cached show data
CREATE TABLE shows (
  id INTEGER PRIMARY KEY,
  title VARCHAR(255) NOT NULL,
  network VARCHAR(100),
  status VARCHAR(50),
  cached_data JSONB,
  last_updated TIMESTAMP DEFAULT NOW()
);
```

## Detailed Setup Instructions

### 1. Supabase Project Setup

#### Create Supabase Account & Project
1. Go to [supabase.com](https://supabase.com) and sign up
2. Click "New Project"
3. Choose organization (create one if needed)
4. Enter project details:
   - **Name:** `tv-show-tracker`
   - **Database Password:** Generate a strong password (save it!)
   - **Region:** Choose closest to your users
5. Wait for project to be created (~2 minutes)

#### Configure Supabase Project
1. In your Supabase dashboard, go to **Settings > API**
2. Copy and save these values:
   - **Project URL** (starts with `https://`)
   - **API Key (anon public)** (starts with `eyJ`)
   - **API Key (service_role)** (starts with `eyJ`) - keep this secret!

#### Set Up Database Schema
1. Go to **SQL Editor** in Supabase dashboard
2. Create the following tables:

```sql
-- User shows tracking table
CREATE TABLE user_shows (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES auth.users(id) ON DELETE CASCADE,
  show_id INTEGER NOT NULL,
  status VARCHAR(20) CHECK (status IN ('watching', 'completed', 'plan_to_watch')),
  rating INTEGER CHECK (rating >= 1 AND rating <= 5),
  date_added TIMESTAMP DEFAULT NOW(),
  date_updated TIMESTAMP DEFAULT NOW(),
  UNIQUE(user_id, show_id)
);

-- Cached show data from TVMaze
CREATE TABLE shows (
  id INTEGER PRIMARY KEY,
  title VARCHAR(255) NOT NULL,
  network VARCHAR(100),
  status VARCHAR(50),
  genres TEXT[],
  summary TEXT,
  image_url TEXT,
  cached_data JSONB,
  last_updated TIMESTAMP DEFAULT NOW()
);

-- Enable Row Level Security
ALTER TABLE user_shows ENABLE ROW LEVEL SECURITY;
ALTER TABLE shows ENABLE ROW LEVEL SECURITY;

-- RLS Policies for user_shows
CREATE POLICY "Users can view own shows" ON user_shows
  FOR SELECT USING (auth.uid() = user_id);

CREATE POLICY "Users can insert own shows" ON user_shows
  FOR INSERT WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can update own shows" ON user_shows
  FOR UPDATE USING (auth.uid() = user_id);

CREATE POLICY "Users can delete own shows" ON user_shows
  FOR DELETE USING (auth.uid() = user_id);

-- RLS Policies for shows (read-only for all authenticated users)
CREATE POLICY "Authenticated users can view shows" ON shows
  FOR SELECT USING (auth.role() = 'authenticated');

-- Allow service role to manage shows cache
CREATE POLICY "Service role can manage shows" ON shows
  FOR ALL USING (auth.jwt() ->> 'role' = 'service_role');
```

### 2. Google OAuth Configuration

#### Set Up Google Cloud Project
1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project or select existing one:
   - Click the project dropdown at the top
   - Click "New Project"
   - **Project Name:** `TV Show Tracker`
   - Click "Create"

#### Enable Required APIs
1. In Google Cloud Console, go to **APIs & Services > Library**
2. Search for and enable:
   - **Google Identity API** (or Google+ API)
   - Click "Enable"

#### Create OAuth 2.0 Credentials
1. Go to **APIs & Services > Credentials**
2. Click "Create Credentials" > "OAuth client ID"
3. If prompted, configure OAuth consent screen first:
   - **User Type:** External (for public use)
   - **App Name:** TV Show Tracker
   - **User support email:** Your email
   - **Developer contact email:** Your email
   - **Scopes:** Add `../auth/userinfo.email` and `../auth/userinfo.profile`
   - **Test users:** Add your email for testing
4. Create OAuth client ID:
   - **Application Type:** Web application
   - **Name:** TV Show Tracker Web Client
   - **Authorized JavaScript origins:**
     - `http://localhost:5173` (for local development)
     - `https://your-project-ref.supabase.co` (replace with your Supabase URL)
     - Add your custom domain if you have one
   - **Authorized redirect URIs:**
     - `https://your-project-ref.supabase.co/auth/v1/callback` (replace with your Supabase URL)
5. Save the **Client ID** and **Client Secret**

#### Configure Supabase Authentication
1. In Supabase dashboard, go to **Authentication > Providers**
2. Find **Google** provider and click configure
3. Enable the Google provider
4. Enter your Google OAuth credentials:
   - **Client ID:** From Google Cloud Console
   - **Client Secret:** From Google Cloud Console
5. Click "Save"

#### Configure Auth Settings
1. In Supabase, go to **Authentication > Settings**
2. Configure these settings:
   - **Site URL:** `http://localhost:5173` (for development)
   - **Redirect URLs:** Add your production domain when ready
   - **JWT Expiry:** 3600 seconds (1 hour) recommended
3. Click "Save"

### 3. Frontend Hosting Setup in Supabase

#### Enable Static Hosting (if available)
1. In Supabase dashboard, check if **Storage** or **Hosting** is available
2. If not available, you can use Netlify for frontend hosting:

#### Alternative: Netlify Frontend Hosting
1. Build your React app: `npm run build`
2. Go to [netlify.com](https://netlify.com) and sign up
3. Drag and drop your `dist` folder to deploy
4. Update your Google OAuth redirect URLs to include Netlify domain
5. Update Supabase Site URL to your Netlify domain

### 4. Edge Functions Deployment

#### Install Supabase CLI
```bash
# Install globally
npm install -g supabase

# Or use npx for project-specific usage
npx supabase --version
```

#### Initialize and Deploy Functions
```bash
# In your project root
supabase init

# Link to your Supabase project
supabase link --project-ref your-project-ref

# Create Edge Functions
supabase functions new search-shows
supabase functions new manage-user-shows

# Deploy functions
supabase functions deploy search-shows
supabase functions deploy manage-user-shows
```

## Development Workflow

### Local Development
```bash
# Start frontend
npm run dev

# Start backend (in separate terminal)
npm run server

# Database (Supabase handles hosting)
# Access via Supabase dashboard
```

### 5. Environment Variables Setup

#### Frontend Environment Variables
Create `.env.local` in your React project root:

```env
# Frontend (.env.local)
VITE_SUPABASE_URL=https://your-project-ref.supabase.co
VITE_SUPABASE_ANON_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

# Optional: Custom API endpoints
VITE_TVMAZE_API_URL=https://api.tvmaze.com
```

#### Edge Functions Environment Variables
In your Supabase project, set secrets for Edge Functions:

```bash
# Set environment variables for Edge Functions
supabase secrets set SUPABASE_URL=https://your-project-ref.supabase.co
supabase secrets set SUPABASE_SERVICE_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
supabase secrets set TVMAZE_API_URL=https://api.tvmaze.com

# List all secrets to verify
supabase secrets list
```

#### Production Environment Variables
For production deployment, update these in:
1. **Netlify:** Site settings > Environment variables
2. **Supabase:** Already configured via secrets above

### 6. Production Deployment Steps

#### Deploy Frontend to Netlify
1. Build your React app:
   ```bash
   npm run build
   ```

2. Deploy to Netlify:
   - Option A: Drag & drop `dist` folder to Netlify
   - Option B: Connect GitHub repo for auto-deployment

3. Configure Netlify:
   - **Build command:** `npm run build`
   - **Publish directory:** `dist`
   - **Environment variables:** Add your Supabase keys

4. Update Google OAuth URLs:
   - Add your Netlify domain to authorized origins
   - Add Netlify domain to Supabase Site URL

#### Verify Edge Functions Deployment
```bash
# Test your deployed functions
curl -X POST 'https://your-project-ref.supabase.co/functions/v1/search-shows' \
  -H 'Authorization: Bearer YOUR_ANON_KEY' \
  -H 'Content-Type: application/json' \
  -d '{"query": "breaking bad"}'
```

### 7. Security Checklist

#### Supabase Security
- ✅ Enable Row Level Security (RLS) on all tables
- ✅ Configure proper RLS policies
- ✅ Use anon key for frontend, service key only for Edge Functions
- ✅ Set up proper CORS settings

#### Google OAuth Security
- ✅ Restrict OAuth credentials to specific domains
- ✅ Use HTTPS in production
- ✅ Regularly rotate client secrets
- ✅ Monitor OAuth usage in Google Cloud Console

### 8. Testing Your Setup

#### Test Authentication Flow
1. Start your local development server
2. Try Google sign-in
3. Check Supabase Authentication dashboard for new users
4. Verify JWT tokens are working

#### Test Database Connectivity
1. Try adding a show to your watchlist
2. Check Supabase Table Editor for new rows
3. Verify RLS policies are working (users can only see their own data)

#### Test TVMaze API Integration
1. Search for a TV show
2. Verify show data is cached in your `shows` table
3. Check rate limiting is working properly

## Cost Projections (Selected Stack)

### Supabase Full-Stack
#### Development Phase: $0/month
- Supabase: Free tier (all services included)
- TVMaze API: Free
- Google OAuth: Free

#### Small Production (< 1000 users): $0/month
- Supabase: Free tier covers up to 50k MAUs
- All services (hosting, database, auth, functions) included

#### Growth Phase (1000+ users): $25/month
- Supabase: Pro tier ($25/month) includes all services
- Single platform, predictable pricing

## Risk Mitigation

### API Rate Limiting
- Implement caching for TVMaze API calls
- Use TMDB as backup data source
- Cache show data in Supabase to reduce API calls

### Free Tier Limitations
- Monitor Supabase usage closely
- Implement efficient queries to minimize database operations
- Set up usage alerts

### Scalability Concerns
- Database designed for horizontal scaling
- API can be scaled independently
- Frontend served via CDN

## Success Metrics

### Technical Metrics
- Page load time < 3 seconds on mobile
- API response time < 500ms
- 99.9% uptime
- Mobile-first responsive design

### User Metrics
- User registration and retention
- Show searches per user
- Rating engagement
- Mobile vs desktop usage

## Pre-Development Considerations

### Required Accounts & Setup
1. **Google Cloud Console** - Create project and OAuth credentials
2. **Supabase Account** - Create project and configure authentication
3. **Netlify Account** - For frontend hosting (free tier)
4. **Domain Registration** (Optional) - For custom domain in production

### Analytics & Monitoring
- **Analytics:** Use Supabase built-in analytics (included in dashboard)
- **Error Monitoring:** Can be added later if needed
- **PWA Features:** Not implementing offline functionality initially

### Development Environment
- **Node.js** (v18+ recommended for Edge Functions compatibility)
- **Git** for version control
- **VS Code** with Supabase and React extensions recommended

### Mobile-First Design Considerations with Ant Design
- Ant Design provides excellent mobile responsiveness out of the box
- Use `Grid` and `Layout` components for responsive design
- Leverage `Drawer` component for mobile navigation
- Consider `List` component for show displays on mobile
- Test on various screen sizes during development

### TVMaze API Integration Notes
- No API key required, but implement caching to respect rate limits
- Cache frequently searched shows in Supabase database
- Handle network errors gracefully with fallback UI states
- Consider implementing search debouncing to reduce API calls

## Next Steps

1. Set up development environment and basic project structure
2. Implement authentication and basic show search
3. Build user show management features
4. Add enhanced features and mobile optimization
5. Deploy to production and optimize performance

This implementation plan provides a clear roadmap to build a feature-complete TV show tracking application while maintaining cost efficiency and modern development practices.