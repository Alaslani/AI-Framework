# FitTrack Master Reference v1.8

**Version**: 1.8 | **Date**: 2026-01-30 | **Status**: Beta

---

## Quick Reference

| Item | Value |
|------|-------|
| Repository | https://github.com/company/fittrack-mobile |
| iOS TestFlight | https://testflight.apple.com/join/xyz |
| Android Beta | Internal testing track |
| API Base | https://api.fittrack.io/v1 |
| Docs | Notion workspace |

### Key Commands

```bash
# iOS development
npx expo run:ios

# Android development
npx expo run:android

# Run tests
npm test

# Build for production
eas build --platform all

# Submit to stores
eas submit
```

---

## Architecture Overview

FitTrack is a cross-platform fitness tracking app built with React Native and Expo. Users log workouts, track nutrition, and view progress analytics. Syncs with Apple Health and Google Fit.

### Tech Stack

| Layer | Technology |
|-------|------------|
| Framework | React Native 0.73, Expo SDK 50 |
| Navigation | Expo Router (file-based) |
| State | Zustand + React Query |
| Styling | NativeWind (Tailwind for RN) |
| Backend | Node.js API (separate repo) |
| Auth | Clerk (OAuth + biometrics) |
| Analytics | Mixpanel |

### Key Patterns

- **Offline-first** — SQLite for local storage, sync when online
- **Optimistic updates** — UI updates immediately, server sync in background
- **Feature flags** — Statsig for A/B testing
- **Deep linking** — Universal links for sharing workouts

---

## Key Decisions

| # | Decision | Rationale | Date |
|---|----------|-----------|------|
| 1 | Expo over bare RN | Faster iteration, EAS builds, OTA updates | 2026-01-02 |
| 2 | Zustand over Redux | Simpler API, less boilerplate | 2026-01-05 |
| 3 | Clerk over Auth0 | Better mobile SDK, biometric support | 2026-01-08 |
| 4 | SQLite over AsyncStorage | Need relational queries for workout data | 2026-01-10 |
| 5 | NativeWind over StyleSheet | Consistent with web, utility-first | 2026-01-12 |

---

## Learnings

| # | Learning | Category |
|---|----------|----------|
| 1 | Expo Router needs `_layout.tsx` in every route group | Gotcha |
| 2 | Use `expo-secure-store` for tokens, not AsyncStorage | Security |
| 3 | Apple Health permissions must be requested on main thread | Gotcha |
| 4 | Android back gesture needs custom handling in modals | Pattern |
| 5 | Use `useFocusEffect` not `useEffect` for screen focus | Pattern |
| 6 | Hermes engine required for large list performance | Pattern |
| 7 | Test on real devices — simulators hide performance issues | Anti-pattern |
| 8 | Use `expo-haptics` sparingly — battery drain | Pattern |

---

## External Integrations

| Service | Purpose | Docs |
|---------|---------|------|
| Apple Health | Sync workouts, steps | Apple HealthKit docs |
| Google Fit | Sync workouts, steps | Google Fit REST API |
| Clerk | Authentication | clerk.com/docs |
| RevenueCat | Subscriptions | revenuecat.com/docs |

---

## Cross-References

| Document | Purpose |
|----------|---------|
| TRANSFER_PACK.md | Session handoff |
| api-backend/ | Backend API reference |

---

*Updated: 2026-01-30*
