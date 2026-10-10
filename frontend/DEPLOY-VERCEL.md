# Deploying Sehat Saathi to Vercel

Sehat Saathi is a React Native + Expo mobile app. The **web** build runs in any
browser and is suitable for Vercel. Native-only features (scheduled push
notifications) are automatically no-ops on web; the camera scanner falls back
to the browser camera / file picker.

## 1. Deploy the backend first (required)

Vercel hosts the **frontend only**. The FastAPI backend (`/app/backend`) and
MongoDB must be deployed separately. Recommended free-tier combo:

| Piece | Service | Notes |
|-------|---------|-------|
| FastAPI backend | **Render.com** / **Railway.app** / **Fly.io** | Python runtime; copy `backend/` as a web service |
| MongoDB | **MongoDB Atlas** (free M0 cluster) | Set `MONGO_URL` in the backend host |
| Gemini AI key | pass through | Set `EMERGENT_LLM_KEY` env on the backend host |

Once the backend is live, note its public HTTPS URL
(e.g. `https://sehat-saathi-api.onrender.com`).

## 2. Push this repo to GitHub

Use Emergent's **Save to GitHub** button, or `git push` manually.

## 3. Import to Vercel

1. Go to <https://vercel.com/new> and import the GitHub repo.
2. **Root directory**: `frontend`
3. **Framework preset**: *Other* (Vercel will read `frontend/vercel.json`).
4. Add environment variable:
   - `EXPO_PUBLIC_BACKEND_URL` = `https://your-backend.example.com`
5. Click **Deploy**.

Vercel will run `yarn build:web` which produces a static web bundle in `dist/`.

## 4. Verify

Open the Vercel URL on desktop or phone browser:

- ✅ Onboarding, language picker, home dashboard, medicines, calendar, daily
  record, assistant chat (text) all work.
- ✅ Scan medicine uses the browser camera / file picker.
- ❌ Scheduled push reminders are disabled on web (platform limitation).
- ❌ Expo Go QR code does not apply here — this is a web deployment.

## 5. Local test before deploying

```bash
cd frontend
yarn install
EXPO_PUBLIC_BACKEND_URL=https://your-backend.example.com yarn build:web
npx serve dist
```

Open `http://localhost:3000` to preview the production web build.

## Notes & limitations

- Vercel is **not** for distributing the mobile app. For real APK/IPA builds
  use Emergent's **Publish → Generate Android/iOS build** flow.
- If `EXPO_PUBLIC_BACKEND_URL` is empty, API calls will fail with network
  errors. Always set it in Vercel's Environment Variables panel.
- CORS is already open on the FastAPI backend (`allow_origins=["*"]`).
