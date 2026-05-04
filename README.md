# Dr. Arjun Mehta — Professional Portfolio Website
### Full-Stack Production Application · Built with Design Thinking

---

## Table of Contents
1. [Project Overview](#project-overview)
2. [Tech Stack](#tech-stack)
3. [Folder Structure](#folder-structure)
4. [Installation & Setup](#installation--setup)
5. [API Reference](#api-reference)
6. [Deployment Guide](#deployment-guide)
7. [Admin Dashboard](#admin-dashboard)
8. [Design Thinking Documentation](#design-thinking-documentation)

---

## Project Overview

A production-ready portfolio website for an AI Research Scientist, featuring:
- Public portfolio with 6 sections (Home, About, Projects, Workshops, Achievements, Contact)
- Secure admin dashboard for full CRUD management
- REST API backend with JWT authentication
- MongoDB database with proper schema design
- Email notifications for contact form submissions

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React.js, Tailwind CSS, Google Fonts |
| Backend | Node.js, Express.js |
| Database | MongoDB (Mongoose ODM) |
| Auth | JWT (jsonwebtoken) |
| Validation | express-validator |
| Email | Nodemailer (optional) |
| Security | Helmet, CORS, Rate Limiting |
| Deployment | Vercel (FE), Render (BE), MongoDB Atlas (DB) |

---

## Folder Structure

```
portfolio/
├── frontend/                  ← React Application
│   ├── public/
│   │   ├── index.html
│   │   └── favicon.ico
│   ├── src/
│   │   ├── components/
│   │   │   ├── Nav.jsx
│   │   │   ├── Hero.jsx
│   │   │   ├── About.jsx
│   │   │   ├── Projects.jsx
│   │   │   ├── Workshops.jsx
│   │   │   ├── Achievements.jsx
│   │   │   ├── Contact.jsx
│   │   │   ├── Footer.jsx
│   │   │   └── admin/
│   │   │       ├── AdminLogin.jsx
│   │   │       └── AdminDashboard.jsx
│   │   ├── services/
│   │   │   └── api.js         ← Axios API calls
│   │   ├── hooks/
│   │   │   └── useAuth.js
│   │   ├── App.jsx
│   │   ├── main.jsx
│   │   └── index.css
│   ├── package.json
│   └── vite.config.js
│
├── backend/                   ← Express REST API
│   ├── config/
│   │   └── db.js              ← MongoDB connection
│   ├── middleware/
│   │   ├── auth.js            ← JWT protect middleware
│   │   └── errorHandler.js    ← Global error handling
│   ├── models/
│   │   ├── Admin.js           ← Admin schema
│   │   ├── Project.js         ← Project schema
│   │   ├── Achievement.js     ← Achievement schema
│   │   └── Workshop.js        ← Workshop + Contact schemas
│   ├── routes/
│   │   ├── auth.js            ← /api/auth
│   │   ├── projects.js        ← /api/projects
│   │   ├── achievements.js    ← /api/achievements + workshops
│   │   └── contacts.js        ← /api/contact
│   ├── scripts/
│   │   └── seed.js            ← Database seeder
│   ├── server.js              ← Entry point
│   ├── package.json
│   └── .env.example
│
└── README.md
```

---

## Installation & Setup

### Prerequisites
- Node.js >= 18.0.0
- npm >= 9.0.0
- MongoDB Atlas account (free tier works)
- Git

### Step 1 — Clone the Repository
```bash
git clone https://github.com/your-username/portfolio.git
cd portfolio
```

### Step 2 — Backend Setup
```bash
cd backend
npm install

# Configure environment
cp .env.example .env
# Edit .env with your MongoDB URI and JWT secret
```

**Minimum .env configuration:**
```env
NODE_ENV=development
PORT=5000
MONGO_URI=mongodb+srv://username:password@cluster.mongodb.net/portfolio
JWT_SECRET=your_minimum_32_char_secret_key_here_abc123
JWT_EXPIRES_IN=7d
```

**Seed the database:**
```bash
npm run seed
# Creates admin user + sample data
# Admin: admin@portfolio.dev / Admin@123
```

**Start the backend:**
```bash
npm run dev      # Development (with nodemon)
npm start        # Production
```

API available at: `http://localhost:5000`

### Step 3 — Frontend Setup
```bash
cd ../frontend
npm install
```

Create `.env.local`:
```env
VITE_API_URL=http://localhost:5000/api
```

**Start the frontend:**
```bash
npm run dev      # Development server
npm run build    # Production build
npm run preview  # Preview production build
```

App available at: `http://localhost:3000`

### Step 4 — Verify Setup
1. Open `http://localhost:3000` — portfolio should load
2. Navigate to `/admin` — login with `admin@portfolio.dev` / `Admin@123`
3. Verify all sections load correctly

---

## API Reference

### Authentication

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| POST | `/api/auth/login` | No | Login → returns JWT |
| GET | `/api/auth/me` | JWT | Get current admin |
| POST | `/api/auth/logout` | JWT | Logout |

**Login request body:**
```json
{
  "email": "admin@portfolio.dev",
  "password": "Admin@123"
}
```

**Login response:**
```json
{
  "success": true,
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "admin": { "id": "...", "email": "admin@portfolio.dev", "name": "Dr. Arjun Mehta" }
}
```

---

### Projects

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/api/projects` | No | List all (supports `?category=&status=&featured=true`) |
| GET | `/api/projects/:id` | No | Single project |
| POST | `/api/projects` | JWT | Create |
| PUT | `/api/projects/:id` | JWT | Update |
| DELETE | `/api/projects/:id` | JWT | Delete |

---

### Achievements

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/api/achievements` | No | List all (supports `?type=award`) |
| POST | `/api/achievements` | JWT | Create |
| PUT | `/api/achievements/:id` | JWT | Update |
| DELETE | `/api/achievements/:id` | JWT | Delete |

---

### Workshops

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/api/workshops` | No | List all active |
| POST | `/api/workshops` | JWT | Create |
| PUT | `/api/workshops/:id` | JWT | Update |
| DELETE | `/api/workshops/:id` | JWT | Delete |

---

### Contact

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| POST | `/api/contact` | No | Submit contact form |
| GET | `/api/contact` | JWT | View all submissions |
| PATCH | `/api/contact/:id` | JWT | Mark read/replied |
| DELETE | `/api/contact/:id` | JWT | Delete submission |

---

## Database Schema

### Project
```js
{
  title: String (required),
  description: String (required),
  techStack: [String] (required),
  category: Enum['AI/ML Research', 'Open Source', ...],
  status: Enum['Active', 'Published', 'Production', 'Beta'],
  year: String,
  githubUrl: String (URL),
  demoUrl: String (URL),
  highlights: [String],
  featured: Boolean,
  order: Number,
  timestamps: true
}
```

### Contact Submission
```js
{
  name: String (required),
  email: String (required, validated),
  subject: String (required),
  message: String (20-5000 chars),
  phone: String (optional),
  organization: String (optional),
  inquiryType: Enum['general','consulting','research','workshop','speaking'],
  isRead: Boolean (default: false),
  isReplied: Boolean (default: false),
  ipAddress: String,
  timestamps: true
}
```

---

## Deployment Guide

### Deploy Frontend → Vercel

1. Push code to GitHub
2. Go to [vercel.com](https://vercel.com) → New Project
3. Import your GitHub repo, select `frontend/` as root directory
4. Set environment variables:
   ```
   VITE_API_URL = https://your-backend.onrender.com/api
   ```
5. Deploy → get URL like `https://portfolio-arjun.vercel.app`

### Deploy Backend → Render

1. Go to [render.com](https://render.com) → New Web Service
2. Connect GitHub repo, set root directory to `backend/`
3. Build command: `npm install`
4. Start command: `node server.js`
5. Set environment variables:
   ```
   NODE_ENV = production
   PORT = 10000
   MONGO_URI = mongodb+srv://...
   JWT_SECRET = your_long_secret_key
   CORS_ORIGIN = https://portfolio-arjun.vercel.app
   ```
6. Deploy → get URL like `https://portfolio-api.onrender.com`

### MongoDB Atlas Setup

1. Create account at [cloud.mongodb.com](https://cloud.mongodb.com)
2. Create a free M0 cluster
3. Create database user with read/write access
4. Whitelist IP addresses (0.0.0.0/0 for Render)
5. Get connection string: `mongodb+srv://user:pass@cluster.mongodb.net/portfolio`
6. Run seed: `MONGO_URI=... npm run seed`

---

## Admin Dashboard

Access at `/admin` on your deployed URL.

**Default credentials:** `admin@portfolio.dev` / `Admin@123`  
*(Change these immediately in production!)*

**Features:**
- Overview dashboard with stats
- Projects: Add, edit, delete projects with all fields
- Achievements: Manage awards, certifications, publications
- Workshops: Manage workshop listings and dates
- Contact Submissions: View, mark read/replied, delete

**Security features:**
- JWT authentication (7-day expiry)
- Account lockout after 5 failed attempts (30 min)
- Rate limiting on login endpoint (10 req/15min)
- Password hashing with bcrypt (12 salt rounds)

---

## Design Thinking Documentation

### STAGE 1 — EMPATHY

**Target Users Defined:**

1. **University Students / Research Scholars** — Look for mentorship, research collaboration, or internship guidance. Need to quickly understand the researcher's background and current work areas.

2. **Technical Recruiters & HRs** — Assess candidate profile for senior AI roles. Need clear evidence of achievements, tech stack, and project impact metrics.

3. **Industry Professionals (Startups / Enterprises)** — Evaluate potential for consulting engagements or workshop bookings. Focus on credibility, domain expertise, and past clients.

4. **Academic Peers / Research Collaborators** — Seek potential co-authors or collaborators. Interested in publication record, research focus, and research gaps being explored.

**User Interview Summaries (Simulated):**

1. **Ananya (PhD Student, IISc):** "I need to quickly scan if this professor's research aligns with mine before reaching out. Long CVs are frustrating — I want a visual summary." Pain point: Dense text, hard to skim.

2. **Daniel (Technical Recruiter, Bangalore):** "I look at GitHub links and project outcomes first. If there's no measurable impact, I move on." Pain point: Lack of quantified project outcomes.

3. **Meera (CTO, AI Startup):** "I want to know if they've actually deployed ML in production — not just academic research. Case studies matter." Pain point: Gap between research claims and production evidence.

4. **Prof. Raman (CS Department, IIT):** "Easy to check their publication list, citation count, and whether they're active. Google Scholar is sufficient." Pain point: Scattered information across multiple platforms.

5. **Vikram (HR, Fortune 500 MNC):** "Mobile-friendly is crucial — I review profiles on my phone between meetings." Pain point: Desktop-only portfolios.

6. **Shruti (CS Undergraduate):** "Workshop details and booking info are hard to find. I once missed a workshop because the registration page was buried." Pain point: Unclear CTAs for actionable items like booking.

7. **Alex (VC Analyst, Tech Fund):** "For due diligence, I need verifiable credentials — certifications, patents, publications with DOIs." Pain point: No way to verify credentials quickly.

**Pain Points Summary:**
- ✗ Too much text, insufficient visual hierarchy
- ✗ No quantified project outcomes
- ✗ Gap between academic research and production deployment evidence
- ✗ Poor mobile experience
- ✗ Workshop/consulting CTAs hard to find
- ✗ No credential verification links
- ✗ Slow navigation across sections

---

### STAGE 2 — DEFINE

**Problem Statement:**

> *Dr. Arjun Mehta, an accomplished AI researcher and practitioner, needs a single unified digital presence that communicates his research credibility, production engineering experience, and consulting expertise to diverse audiences — students, recruiters, industry professionals, and academic peers — within 30 seconds of landing, without requiring them to navigate multiple platforms or read through dense CVs.*

**User Persona:** Dr. Mehta's audience is time-constrained, achievement-oriented, and highly technical. They need *scannable evidence* over *readable prose*.

**Key Design Challenges:**
1. How do we present academic depth without losing non-academic visitors?
2. How do we balance project showcase (engineering) with research credibility (academia)?
3. How do we make workshop booking and consulting inquiry effortless?
4. How do we create an admin experience that a researcher can manage without technical assistance?

---

### STAGE 3 — IDEATION

**Brainstorming Session (Output):**

Ideas generated using the "How Might We" framework:

- HMW show impact quickly? → Hero stats (publications, students, citations)
- HMW serve multiple audiences? → Role-based content filtering ("I'm a...") 
- HMW make navigation fast? → Single-page scrolling with sticky nav
- HMW prove production credentials? → Status badges on projects (Published/Production/Active)
- HMW simplify workshop discovery? → Upcoming dates prominently visible on workshop cards
- HMW reduce contact friction? → Prominent contact form + social links, multiple entry points
- HMW maintain content? → Admin dashboard with full CRUD, no code required

**Mind Map — Portfolio Website:**
```
Portfolio Website
├── Discovery (Hero)
│   ├── Name + Title
│   ├── Animated role display
│   ├── Social proof stats
│   └── Primary CTAs
├── Credibility (About + Achievements)
│   ├── Education timeline
│   ├── Skills grid by category
│   ├── Publications (with citations)
│   └── Certifications + Awards
├── Work Showcase (Projects)
│   ├── Filter by category
│   ├── Status indicators
│   ├── Impact metrics (badges)
│   └── GitHub + Demo links
├── Commercial (Workshops + Consulting)
│   ├── Workshop cards with booking
│   ├── Consulting service packages
│   └── Direct contact CTAs
└── Connection (Contact)
    ├── Contact form (stored in DB)
    ├── Social media links
    └── Response time expectation
```

**Solution Ideas Evaluated:**

| Idea | Pros | Cons | Decision |
|---|---|---|---|
| Multi-page React App (React Router) | Familiar UX, SEO-friendly | Complex routing, slower nav | ✓ Adopt for production |
| Single-page scroll | Fast, fluid experience | Poor SEO for sections | ✓ Use for sections |
| Role-based content filter | Personalized experience | Adds UI complexity | Deferred to v2 |
| Dark theme | Distinctive, memorable | Accessibility concern | ✓ Adopt (test contrast) |
| PDF resume download | Expected by recruiters | Maintenance burden | ✓ Adopt (link to PDF) |
| Admin via CMS (Contentful) | Easy editing | Cost, dependency | ✗ Build custom admin |

---

### STAGE 4 — PROTOTYPE

**UI/UX Decisions:**

1. **Dark Academic Aesthetic:** Chosen to convey intellectual depth and technical sophistication. Deep navy (#070E1A) with warm gold (#C9A84C) creates a premium feel without being corporate. Contrast ratios meet WCAG AA.

2. **Typography Pairing:**
   - *Cormorant Garamond* (display headings) → Academic gravitas, distinctive character
   - *Outfit* (body) → Clean readability, modern sans-serif
   - *JetBrains Mono* (code/tech) → Technical authenticity

3. **Navigation Strategy:** Sticky top nav with scroll detection. Section-based scrolling for single-page feel + URL-friendly routing for admin.

4. **Projects Filter:** Tabbed filter with "All" default. Status badges (color-coded: gold=Published, green=Production, blue=Active) provide instant visual scanning.

5. **Hero Typing Animation:** Rotates through professional titles to serve multiple audiences without cluttering the headline.

6. **Contact Form:** Two-column layout with contact info on left (builds trust) and form on right (reduces intimidation). Inline validation prevents empty submissions.

7. **Admin Dashboard:** Sidebar navigation mirrors common admin UIs (Notion, Vercel). Tables for data management, modals for add/edit forms.

**Wireframe Descriptions:**

*Hero:* Full-viewport section. Left-aligned content (60% width). Background: animated gradient orbs + subtle grid pattern. Hierarchy: eyebrow → name (large serif) → typed role → tagline → CTAs → stats.

*Projects:* Filter tabs centered. Card grid (3 cols desktop, 2 cols tablet, 1 col mobile). Each card: colored thumbnail with status badge → title → description → tech tags → action links.

*Admin Dashboard:* Fixed sidebar (240px) + scrollable main content. Overview shows stats grid + recent contact table. Each entity page shows sortable table + add/edit modal.

---

### STAGE 5 — TESTING

**Simulated Usability Test Results (5 Users):**

**User 1 — Ananya (PhD Student)**
- Task: Find Dr. Mehta's current research interests
- Result: Found in About section (under bio) and Projects section in under 45 seconds ✓
- Feedback: "The skills tab is really helpful — I could quickly see what tools he uses."
- Improvement suggested: Add research interests as a separate highlighted section

**User 2 — Daniel (Recruiter)**
- Task: Assess suitability for a Principal ML Engineer role
- Result: Viewed Projects, noticed status badges, clicked GitHub link ✓
- Feedback: "The stats in the hero section immediately told me what I needed."
- Improvement suggested: "Years of Experience" should be more prominent

**User 3 — Meera (CTO)**
- Task: Book a consulting call and find workshop details
- Result: Found workshop cards with booking CTA in 2 clicks ✓
- Feedback: "I love that the fee and upcoming date are right on the card."
- Improvement suggested: Add a calendar booking integration (Calendly)

**User 4 — Prof. Raman (Academic)**
- Task: Find publications and citation count
- Result: Found in Achievements tab (Publications filter) ✓
- Feedback: "Citation count on the cards is useful. DOI links missing on some."
- Improvement suggested: Add Google Scholar link more prominently

**User 5 — Vikram (HR, Mobile)**
- Task: Navigate the portfolio on iPhone SE
- Result: Navigation worked, contact form submitted successfully ✓
- Feedback: "Some project cards felt a bit cramped on mobile."
- Improvement suggested: Collapse tech tags on mobile to save space

---

**What Worked Well:**
- Dark academic aesthetic stood out positively in all tests
- Hero stats gave instant credibility signal
- Project filter tabs reduced cognitive load
- Contact form validation prevented incomplete submissions
- Admin dashboard was intuitive — all testers could add a project without instruction

**What Needs Improvement (v2 Backlog):**
- Add Calendly integration to workshop booking
- Add Google Scholar profile link in nav or hero
- Add DOI/credential verification links on achievement cards
- Optimize mobile project card layout (collapsed tech tags)
- Add a blog/articles section for thought leadership
- Implement search across all content
- Add role-based content filter ("I'm a student/recruiter/partner")
- Light mode toggle option

---

## Contributing

Pull requests are welcome. For major changes, please open an issue first.

## License

MIT License — See LICENSE file for details.

## Contact

Portfolio: arjun.mehta@iisc.ac.in
