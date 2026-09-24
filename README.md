# Nuvia Tissues — Website

Marketing and shop-front website for **Nuvia**, a Pakistani tissue brand (toilet rolls, facial tissues, kitchen towels, baby care and pocket packs). Built as a single-page React app with a built-in admin panel for editing site content.

![Home page](https://mujtabaasif.vercel.app/assets/projects-screenshots/nuvia/home.webp)

**Live site:** https://nuviatissues.vercel.app/ · **Portfolio:** https://mujtabawd.vercel.app/

## Tech stack

| Area | Choice |
| --- | --- |
| Framework | React 18 + TypeScript |
| Build tool | Vite 5 |
| Styling | Tailwind CSS 3 (Poppins for headings, Inter for body) |
| Icons | lucide-react |
| Hosting | Vercel (`vercel.json` rewrites every path to the SPA) |

`@supabase/supabase-js` is installed but not used yet — all content currently lives in `src/utils/data.ts`.

## Getting started

Requires Node.js 18 or newer.

```bash
npm install
npm run dev        # http://localhost:5173
```

| Script | What it does |
| --- | --- |
| `npm run dev` | Start the Vite dev server with hot reload |
| `npm run build` | Production build into `dist/` |
| `npm run preview` | Serve the production build locally |
| `npm run typecheck` | Type-check with `tsc` |
| `npm run lint` | Run ESLint |

## Project structure

```
src/
├── App.tsx               Router: maps the current page to a component, syncs the URL
├── main.tsx              Entry point
├── context/
│   ├── AppContext.tsx    Current page + params, navigate(), cart, search/cart open state
│   └── AdminContext.tsx  Admin content overrides (saved to localStorage)
├── layouts/              MainLayout, Navigation (header), Footer
├── pages/                One component per page (see "Pages and URLs")
├── sections/             Home-page building blocks (Hero, FAQ, Testimonials, …)
├── components/           Reusable pieces: ProductCard, ArticleCard, CartSidebar, SearchModal, logo
├── ui/                   Primitives: Button, Badge, SectionHeader, CloudDivider
├── hooks/useScrolled.ts
└── utils/
    ├── data.ts           ALL default site content (products, articles, nav, topics, …)
    ├── types.ts          TypeScript interfaces for that content
    ├── useAdminData.ts   Hooks that return admin overrides, falling back to data.ts
    └── routing.ts        URL ⇄ page mapping (resolvePath / buildPath / slugify)
public/images/            Product packshots and team images (SVG)
scripts/generate-images.py  Regenerates the SVG packshots: python3 scripts/generate-images.py public/images
```

## Pages and URLs

Routing is hand-rolled, with no router library. `navigate(page, params)` in `AppContext` changes the page, and `App.tsx` keeps the address bar in sync using `buildPath` and `resolvePath` from [src/utils/routing.ts](src/utils/routing.ts). The browser back and forward buttons work, and every URL below can be opened directly or refreshed.

| URL | Page | Component |
| --- | --- | --- |
| `/` | Home | `HomePage` |
| `/products` | All products | `ProductsPage` |
| `/products/:category` | Products filtered by category, e.g. `/products/kitchen-towels` | `ProductsPage` |
| `/product/:id` | Product detail, e.g. `/product/ultra-quilted` | `ProductDetailPage` |
| `/articles` | All articles | `ArticlesPage` |
| `/articles/category/:category` | Articles filtered by category, e.g. `/articles/category/home-care` | `ArticlesPage` |
| `/articles/:id` | Article detail | `ArticleDetailPage` |
| `/hygiene-care`, `/family-life` | Topic hub pages | `TopicHubPage` |
| `/hygiene-care/:topic`, `/family-life/:topic` | Topic pages, e.g. `/hygiene-care/hand-hygiene` | `TopicPage` |
| `/sustainability` | Sustainability | `SustainabilityPage` |
| `/our-story` | About / our story | `OurStoryPage` |
| `/contact` | Contact form, FAQs, office hours | `ContactPage` |
| `/admin` | Content admin panel | `AdminPage` |

Unknown URLs fall back to the home page.

`navigate()` accepts either a page key with params (`navigate('products', { category: 'toilet-rolls' })`) or a path (`navigate('/products/toilet-rolls')`). Nav and footer data use the path form, so links can be edited in the admin panel.

## Header navigation

Header links come from `NAV_LINKS` in `data.ts`, and the admin panel can override them.

- **Hygiene & Care** leads to Hand Hygiene, Bathroom Essentials, Facial Care, Baby Care and Baby Carriers.
- **Family Life** leads to Parenting Tips, Healthy Living, Home Organisation and Creative Activities.
- **Products** leads to All Products, Toilet Rolls, Facial Tissues, Kitchen Towels, Pocket Packs and Baby Care.
- **Sustainability**, **Our Story** and **Contact Us** have no sub-menu.

On desktop, the dropdowns open on hover or keyboard focus, and Esc closes them. On mobile, each section expands with its arrow button.

### Adding a topic page

1. Add an entry to `TOPICS` in `src/utils/data.ts` with `hub`, `slug`, `title`, `intro`, `image`, `tips`, `articleIds` and `productCategories`.
2. Add a dropdown item pointing to `/<hub>/<slug>` in `NAV_LINKS`.

The page appears automatically at that URL and on its hub page.

## Content and the admin panel

All default content is in [src/utils/data.ts](src/utils/data.ts). The admin panel at `/admin` can edit products, articles, testimonials, FAQs, navigation links, featured cards, trust badges, sustainability stats and pillars, the timeline, company values, team members, the contact page, office hours, footer columns, categories, search terms and the origin story.

**Important limitations:**

- Admin edits are saved **only in the current browser's `localStorage`** (key `nuvia-admin-overrides`). They are not shared with other visitors or devices. To change content for everyone, edit `data.ts` and redeploy.
- `/admin` has **no login**. Anyone who knows the URL can open it. Since changes only affect their own browser, this is harmless today, but it must be protected before admin edits are saved to a real backend.
- Saved overrides take priority over `data.ts`. If new defaults don't show up, use the reset buttons in the admin panel or clear the site's storage.

## Current feature status

| Feature | Status |
| --- | --- |
| Product catalogue, categories, sorting, grid/list view | Working |
| Product and article detail pages | Working |
| Search (products + articles) | Working |
| Cart (add, change quantity, remove, subtotal) | Working, in memory only — cleared on refresh |
| Checkout button | Not connected yet |
| Contact form, newsletter sign-up | Validate and show a success message; nothing is sent yet |
| Admin panel | Working, browser-local only (see above) |

## Deployment

The site deploys to Vercel as a static build. The build command is `npm run build` and the output folder is `dist`. `vercel.json` rewrites every path to `/` so that deep links like `/hygiene-care/hand-hygiene` load the app. Other static hosts need the same single-page-app fallback rule.

## Brand

| Token | Hex |
| --- | --- |
| Primary teal | `#3B8296` |
| Dark teal | `#2C6878` / `#1f4d59` |
| Light teal | `#EAF6F8` / `#c9ecf3` / `#A7D8E5` |

These are also available as Tailwind `primary-50` … `primary-900` in `tailwind.config.js`.
