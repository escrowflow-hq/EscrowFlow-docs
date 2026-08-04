# EscrowFlow Mobile App

The mobile app (React Native + Expo) is a full-featured client for Android, mirroring the web dashboard with native features like biometric auth and local notifications.

## Installation

**Android (Current):**
- Download the latest APK from [expo.dev/accounts/escrowflows-team/projects/escrowflow/builds](https://expo.dev/accounts/escrowflows-team/projects/escrowflow/builds)
- Enable "Unknown sources" in Settings → Security
- Tap the APK to install
- **Note**: currently testnet only with mock data and faucet testing

**iOS (Testing Only):**
- Expo Go app from the App Store (free)
- Scan the QR code from `npx expo start` to load the live app
- **Note**: full iOS App Store deployment planned for Phase 3, after mainnet launch

## Features

**Authentication:**
- Email + password signup/login with role selection (Client or Freelancer)
- Biometric unlock (fingerprint / Face ID) after first login
- Session expires after 30 min of inactivity (for security)

**Wallet:**
- View balance (available, pending earnings, in escrow)
- Deposit via faucet (testnet; real card/bank in Phase 2)
- Withdraw UI with destination options (bank, mobile money, crypto)
- View transaction history with real tx hashes

**Projects:**
- Create projects with milestones, budget, and freelancer email
- Browse all projects or filter by status (Active, Awaiting deposit, Completed)
- View project detail with 4 tabs: Overview, Milestones, Messages, Files

**Milestones:**
- Submit milestone (as freelancer) when work is complete
- Approve/reject milestone (as client) with feedback
- Track progress bar per milestone
- Automatic payout on approval (minus 3% fee)

**Messages:**
- In-app chat per project (mock in MVP, real WebSocket in Phase 2)
- File upload/download (mock in MVP, real cloud storage in Phase 2)
- Notification badge showing unread count

**Notifications:**
- In-app notification center with activity log
- Push notifications (mock in MVP, real Firebase in Phase 2)
- Toggleable settings per activity type

**KYC:**
- 3-step KYC flow in settings (mock in MVP, real provider in Phase 2)
- Selfie capture with native camera
- Status badge on profile (Not Started → Pending → Approved)

**Settings:**
- Edit profile (name, email, bio)
- Security settings (biometric toggle, password change)
- View Stellar wallet address (copy/share)
- Notification preferences
- Logout

## Architecture

**Stack:**

| Concern | Choice |
|---|---|
| Framework | React Native + Expo SDK 52 |
| Navigation | Expo Router (file-based, native-like tabs) |
| State | Zustand (lightweight, persisted) |
| Data fetching | TanStack Query (caching, background sync) |
| HTTP | Axios (with mock interceptors) |
| Storage | `expo-secure-store` (encrypted), AsyncStorage (public) |
| Native | `expo-local-authentication`, `expo-image-picker`, `expo-document-picker`, `expo-notifications` |

**Mock Data Layer:**
- `USE_MOCK=true` in `.env` → all API calls return hardcoded responses
- Realistic latency: 300–600ms delay per request
- Business rules enforced: approve deducts 3% fee, submit blocked if escrow unfunded, etc.
- Switch to real API: `USE_MOCK=false`, set `API_URL` to the Render backend

**File Structure:**

```
app/
  (auth)/                 # Unauthenticated routes
    login.tsx
    signup.tsx
    onboarding.tsx
  (tabs)/                 # Tab-based layout
    _layout.tsx           # Tab bar + stack navigator
    index.tsx              # Home / Dashboard
    projects/
      _layout.tsx
      [id]/
        index.tsx          # Project detail
        milestones.tsx
        messages.tsx
        files.tsx
    wallet/
      index.tsx
      deposit.tsx
      withdraw.tsx
    settings/
      _layout.tsx
      index.tsx
      profile.tsx
      security.tsx
      wallet-address.tsx
      notifications.tsx
src/
  components/
    ui/                    # Icon, Button, Card, etc.
    layouts/
  constants/
    theme.ts                # Design tokens
  hooks/
    useProjects.ts
    useWallet.ts
  lib/
    mock-service.ts         # Mock API layer
    api-client.ts           # Real API layer
  store/
    auth.store.ts
    app.store.ts
    projects.store.ts
  types/
    index.ts                # User, Project, Milestone, etc.
```

## Development

**Setup:**

```bash
cd EscrowFlow-mobile
npm install
npx expo prebuild --clean  # Build native code
```

**Run locally:**

```bash
# Expo Go (instant, live reload)
npx expo start

# Android emulator
npx expo start --android

# iOS simulator (macOS only)
npx expo start --ios
```

**Environment Variables (`.env`):**

```
USE_MOCK=true                       # true = mock API, false = real API
EXPO_API_URL=http://localhost:3000  # when USE_MOCK=false
```

**Build APK (production):**

```bash
export EXPO_TOKEN=your_token
eas build --platform android --profile preview
# Download APK from build page, install on device
```

## Current Status

**What's Ready (MVP):**
- Full escrow flow (create → fund → submit → approve) ✅
- 3% fee calculation ✅
- Role-based UI (client/freelancer views) ✅
- Mock data layer with testnet USDC simulation ✅
- Android APK build via EAS ✅
- Biometric security ✅

**Coming in Phase 2 (Q4 2026):**
- Real API integration (currently mock)
- Real KYC provider (Stripe Identity or Onfido)
- Real push notifications (Firebase)
- File storage (cloud upload)
- WebSocket chat (real-time)

**Coming in Phase 3 (Q2 2027):**
- iOS App Store release
- Google Play Store release
- Offline mode
- Advanced analytics

See the full [Roadmap](../roadmap.md) for context on these phases.

## Testing

Run tests:

```bash
npm test
```

Coverage:
- Mock service business rules (approve deducts fee, submit blocked if unfunded)
- Navigation (auth guard, tab switching)
- State persistence (values survive app restart)

## Troubleshooting

**App freezes on startup:**
- Uninstall old APK completely
- Restart phone
- Install fresh APK (v1.0.6+)

**Icons not displaying:**
- Ensure the Ionicons font is embedded (`app.json` plugins section)
- Force restart the app
- Check the browser console for font loading errors (in Expo Go, press 'D')

**Biometric not working:**
- Requires a device with fingerprint/Face ID
- On emulator, test with mock (`expo-local-authentication` handles this gracefully)

**Android APK won't install:**
- Check that "Unknown sources" is enabled in Settings
- Uninstall any previous version completely before installing the new APK
