# Phone-desks
Multi-number phone linking app
multilinkpro/
├── server.js              ← entry point, wires everything
├── db.js                  ← in-memory store (swap for Firestore/Postgres)
├── package.json
├── middleware/
│   ├── auth.js            ← JWT authenticate, requireOwner
│   └── validate.js        ← isValidUrl, isValidSlug, paginate, err helpers
├── routes/
│   ├── auth.js            ← register, login, me, change password
│   ├── users.js           ← list, profile, delete
│   ├── pages.js           ← CRUD + slug check
│   ├── links.js           ← CRUD + reorder + click tracking
│   └── analytics.js       ← click stats, top links
└── public/
    ├── index.html         ← landing page
    ├── dashboard.html     ← user dashboard SPA (auth + links + analytics + settings)
    └── page.html          ← public link-in-bio viewer (used for /:slug routes)
Run locally
npm install
npm run dev       # auto-restarts on file changes (nodemon)
# or
npm start         # plain node
Open: http://localhost:3000
URLs
URL
What it is
/
Landing page
/dashboard.html
User dashboard
/sarah-c
Demo public page (seeded)
/mwebb
Demo public page
/api
API endpoint index
/api/health
Health check
Demo accounts (seeded)
Email
Password
sarah@example.com
password123
marcus@example.com
password123
priya@example.com
password123
API quick reference
POST   /api/auth/register         { username, email, password, displayName }
POST   /api/auth/login            { email, password }
GET    /api/auth/me               → current user  [auth]
PUT    /api/auth/me               { displayName, bio }  [auth]
PUT    /api/auth/me/password      { currentPassword, newPassword }  [auth]

GET    /api/pages/slug/:slug      → public page + links (increments views)
GET    /api/pages/slug/:slug/check → { available: bool }
POST   /api/pages                 { slug, title, description, theme, accentColor }  [auth]
PUT    /api/pages/:id             [auth, owner]
DELETE /api/pages/:id             [auth, owner]

GET    /api/links?pageId=p1       → sorted links
POST   /api/links                 { pageId, title, url }  [auth]
PUT    /api/links/reorder         { pageId, order: ["l2","l1","l3"] }  [auth]
PUT    /api/links/:id             { title, url, isActive, sortOrder }  [auth]
DELETE /api/links/:id             [auth]
POST   /api/links/:id/click       → records click, returns { url }  [public]

GET    /api/analytics             → 30-day stats for current user  [auth]
Deploy to Railway
# 1. Push to GitHub
git init && git add . && git commit -m "init"
gh repo create multilinkpro --public --push

# 2. Go to railway.app → New Project → Deploy from GitHub
# Railway auto-detects Node and runs: node server.js
# You get a live URL in ~60 seconds.
Swap to a real database
All data access is in db.js. Each collection (users, pages, links) exposes
a clean interface (findAll, findById, create, update, delete).
Replace those methods with Firestore/Postgres calls and nothing else changes.
