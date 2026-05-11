# AI Fitness Voice Call

An AI-powered fitness application that uses voice conversation to generate personalized workout and diet plans. Users have a natural voice call with an AI assistant that collects their fitness profile, then automatically generates and saves a custom plan to their account.

---

## Features

- **Voice-driven onboarding** — Talk to an AI fitness coach via VAPI to describe your goals, fitness level, injuries, and dietary preferences
- **AI-generated plans** — Gemini 2.0 Flash generates a personalized weekly workout schedule and daily meal plan based on your profile
- **Profile dashboard** — View all your fitness plans, switch between them, and see workout/diet details in a clean tabbed interface
- **Authentication** — Secure sign-in and sign-up powered by Clerk
- **Persistent storage** — Plans saved to Convex database and accessible across sessions

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js 14 (App Router), TypeScript, Tailwind CSS |
| Auth | Clerk |
| Database | Convex |
| Voice AI | VAPI |
| AI Generation | Google Gemini 2.0 Flash |
| UI Components | shadcn/ui |

---

## Project Structure

```
├── convex/
│   ├── http.ts          # VAPI webhook + Gemini plan generation endpoint
│   ├── plans.ts         # Convex mutations/queries for fitness plans
│   ├── schema.ts        # Database schema
│   └── users.ts         # User sync from Clerk webhooks
├── src/
│   ├── app/
│   │   ├── generate-program/  # Voice call page
│   │   ├── profile/           # Plan dashboard page
│   │   └── (auth)/            # Sign-in / Sign-up pages
│   ├── components/
│   │   ├── ui/                # shadcn/ui components
│   │   ├── ProfileHeader.tsx
│   │   ├── NoFitnessPlan.tsx
│   │   └── UserPrograms.tsx
│   ├── lib/
│   │   └── vapi.ts            # VAPI client instance
│   └── providers/
│       └── ConvexClerkProvider.tsx
└── public/
```

---

## Getting Started

### Prerequisites

- Node.js 18+
- A [Clerk](https://clerk.com) account
- A [Convex](https://convex.dev) account
- A [VAPI](https://vapi.ai) account with a configured workflow
- A [Google AI Studio](https://aistudio.google.com) API key (Gemini)

### 1. Clone and install

```bash
git clone <your-repo-url>
cd ai-fitness-voice-call
npm install
```

### 2. Set up environment variables

Create a `.env.local` file in the root:

```env
# Clerk
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=pk_test_...
CLERK_SECRET_KEY=sk_test_...
NEXT_PUBLIC_CLERK_SIGN_IN_URL=/sign-in
NEXT_PUBLIC_CLERK_SIGN_UP_URL=/sign-up

# VAPI
NEXT_PUBLIC_VAPI_API_KEY=your_vapi_api_key
NEXT_PUBLIC_VAPI_WORKFLOW_ID=your_vapi_workflow_id

# Convex
CONVEX_DEPLOYMENT=dev:your-deployment-name
NEXT_PUBLIC_CONVEX_URL=https://your-deployment.convex.cloud
```

### 3. Set up Convex

```bash
npx convex dev
```

This starts the Convex dev server and deploys your schema and functions.

### 4. Set up Clerk webhook

In your Clerk dashboard, create a webhook pointing to:

```
https://your-convex-deployment.convex.site/clerk-webhook
```

Enable the `user.created` and `user.updated` events. Copy the signing secret and add it to your Convex environment variables:

```bash
npx convex env set CLERK_WEBHOOK_SECRET whsec_...
```

### 5. Add Gemini API key to Convex

```bash
npx convex env set GEMINI_API_KEY your_gemini_api_key
```

### 6. Run the app

```bash
npm run dev
```

Visit [http://localhost:3000](http://localhost:3000).

---

## How It Works

1. **User signs up** — Clerk handles auth; a webhook fires to Convex to sync the user record.
2. **Start a voice call** — On the `/generate-program` page, the user clicks "Start Call" to connect with the VAPI AI assistant.
3. **AI collects data** — The VAPI workflow asks questions about age, weight, height, fitness goal, workout days, injuries, and dietary restrictions.
4. **Plan generation** — When the call ends, VAPI sends the collected data to the `/vapi/generate-program` Convex HTTP endpoint, which calls Gemini twice — once for a workout plan and once for a diet plan.
5. **Saved to DB** — The generated plans are validated and saved to Convex. Any previously active plan is deactivated.
6. **View on profile** — The user is redirected to `/profile` where they can view their active plan and switch between past plans.

---

## Environment Variables Reference

| Variable | Where to get it |
|---|---|
| `NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY` | Clerk dashboard → API Keys |
| `CLERK_SECRET_KEY` | Clerk dashboard → API Keys |
| `NEXT_PUBLIC_VAPI_API_KEY` | VAPI dashboard → API Keys |
| `NEXT_PUBLIC_VAPI_WORKFLOW_ID` | VAPI dashboard → your workflow ID |
| `CONVEX_DEPLOYMENT` | Auto-set by `npx convex dev` |
| `NEXT_PUBLIC_CONVEX_URL` | Auto-set by `npx convex dev` |
| `CLERK_WEBHOOK_SECRET` | Clerk dashboard → Webhooks → signing secret (set in Convex) |
| `GEMINI_API_KEY` | Google AI Studio (set in Convex) |

---

## License

MIT