# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

@AGENTS.md

## Commands

```bash
npx expo start          # Start dev server (scan QR for Expo Go, or press i/a for simulator)
npx expo start --ios    # Open directly in iOS simulator
npx expo start --android # Open directly in Android emulator
npx expo start --web    # Open in browser
npx expo lint           # Run ESLint
npm run reset-project   # Move starter code to app-example/ and create a blank src/app/
```

No test runner is configured yet.

## Architecture

**Expo SDK 56** app using **expo-router** for file-based routing. Entry point is `expo-router/entry` (set in `package.json`). All source lives under `src/`.

### Routing (`src/app/`)

expo-router maps the filesystem to routes. `_layout.tsx` wraps all routes with `ThemeProvider` and renders `AppTabs` (the tab bar). Screens are `index.tsx` (Home) and `explore.tsx` (Explore).

Typed routes and the React Compiler are both enabled via `app.json` `experiments`.

### Platform-specific files

Files suffixed `.web.ts` / `.web.tsx` are automatically chosen over their unsuffixed counterparts on web. Current pairs:
- `src/hooks/use-color-scheme.ts` → `.web.ts`
- `src/components/app-tabs.tsx` → `.web.tsx`
- `src/components/animated-icon.tsx` → `.web.tsx` + `.module.css`

### Theme system (`src/constants/theme.ts`)

Single source of truth for design tokens:
- `Colors` — light/dark palettes (`text`, `background`, `backgroundElement`, `backgroundSelected`, `textSecondary`)
- `Fonts` — platform-selected font family strings (iOS system fonts, CSS variables on web)
- `Spacing` — numeric scale (`half`=2 … `six`=64)
- `BottomTabInset`, `MaxContentWidth` — layout constants

Use `useTheme()` from `src/hooks/use-theme.ts` to get the current palette. Wrap text and views in `ThemedText` / `ThemedView` rather than accessing colors directly.

### Tab navigation

Uses `NativeTabs` from `expo-router/unstable-native-tabs` (native implementation). The web counterpart in `app-tabs.web.tsx` provides a different implementation for the browser.

### Path aliases

`@/*` → `src/*` and `@/assets/*` → `assets/*` (configured in `tsconfig.json`).
