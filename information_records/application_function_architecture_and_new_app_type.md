# Application Function Architecture and How to Add a New Application Type

## 1) Current Architecture (Mini Apps / Application Function)

In this project, the "application function" is implemented as the **Mini Apps** subsystem in the renderer process.

### Core layers

1. **Data model (type definition)**
   - `src/renderer/src/types/index.ts` defines `MinAppType`:
     - Required: `id`, `name`, `url`
     - Optional: `nameKey`, `supportedRegions`, `logo`, `style`, `type`, etc.
   - Region visibility is modeled by:
     - `MinAppRegion = 'CN' | 'Global'`
     - `MinAppRegionFilter = 'auto' | 'CN' | 'Global'`

2. **Configuration source**
   - `src/renderer/src/config/minapps.ts`
   - Contains:
     - `ORIGIN_DEFAULT_MIN_APPS`: built-in app catalog
     - `loadCustomMiniApp()`: loads `custom-minapps.json`
     - `allMinApps`: merged list (default + custom)
     - `updateAllMinApps()`: updates in-memory merged list

3. **State storage (Redux)**
   - `src/renderer/src/store/minapps.ts` stores:
     - `enabled`
     - `disabled`
     - `pinned`
   - This slice is integrated via `src/renderer/src/store/index.ts`.

4. **Business hook (read/write behavior + region logic)**
   - `src/renderer/src/hooks/useMinapps.ts`
   - Important design rule: **region filtering is a view concern, not a data concern**.
     - Redux keeps complete app lists.
     - UI reads filtered lists by region.
     - Write operations preserve hidden-region apps to avoid preference loss.

5. **UI layer**
   - Main pages and components under `src/renderer/src/pages/minapps/`:
     - `MinAppsPage.tsx`, `MinAppPage.tsx`
     - `MiniappSettings/MiniAppSettings.tsx`
     - `NewAppButton.tsx` (manual custom app creation form)
     - Toolbar/full-page webview components

### Runtime flow

1. App list is built from defaults + custom JSON.
2. Redux stores enabled/disabled/pinned lists.
3. `useMinapps` computes region-aware visible lists for rendering.
4. User actions (enable/disable/pin/add custom) update Redux and/or custom JSON.

---

## 2) How to Create a New Application Type

The answer depends on what you mean by "new type":

## A) Add a new app entry (same Mini App type) — easiest and already supported

You have two supported approaches:

1. **Runtime custom app (no code change)**
   - Use the UI in Mini App settings (`NewAppButton.tsx`).
   - It writes entries into `custom-minapps.json` with `type: 'Custom'`.

2. **Built-in app (code change)**
   - Add a new object into `ORIGIN_DEFAULT_MIN_APPS` in `src/renderer/src/config/minapps.ts`.
   - Include at minimum:
     - unique `id`
     - display `name`
     - `url`
   - Recommended:
     - `supportedRegions`
     - `logo`
     - `nameKey` for i18n if needed.

Example shape:

```ts
{
  id: 'my-app',
  name: 'My App',
  url: 'https://example.com',
  logo: MyAppLogo,
  supportedRegions: ['CN', 'Global']
}
```

## B) Add a truly new "application category/type" (beyond `Custom | Default`)

Today, `MinAppType['type']` is limited to:

```ts
type?: 'Custom' | 'Default'
```

If you want a new category (for example `Internal`, `Plugin`, `Enterprise`), you need coordinated changes:

1. **Expand type union**
   - Edit `src/renderer/src/types/index.ts`:
   - `type?: 'Custom' | 'Default' | 'Internal'` (example)

2. **Populate that type in data source**
   - Set `type: 'Internal'` where apps are created or loaded:
     - `src/renderer/src/config/minapps.ts`
     - `src/renderer/src/pages/minapps/NewAppButton.tsx` (if user-created path should support it)

3. **Define behavior rules for the new type**
   - Decide whether this type differs in:
     - visibility
     - reorderability
     - pinning
     - edit permissions
   - Implement in `src/renderer/src/hooks/useMinapps.ts` and/or settings components.

4. **Update UI filtering/labels**
   - Add labels/sections or badges in minapps pages/settings if type-specific display is required.

5. **Add/adjust migration logic if needed**
   - Historical app lists are migrated in `src/renderer/src/store/migrate.ts`.
   - If semantics change, update migration logic so old persisted state remains valid.

6. **Add tests**
   - Extend existing minapps tests (store/hook/UI) to cover new type behavior.

---

## 3) Important Repository Constraint

`src/renderer/src/store/minapps.ts` is marked as **v2 refactor blocked** (critical-fix-only).

So for safe changes now:

- Prefer adding app entries via config or custom JSON/UI.
- Avoid large feature-level changes in blocked files unless you are working on the designated v2 branch/process.

---

## 4) Practical Recommendation

If your goal is simply "add another application": use **Option A**.

If your goal is "introduce a new lifecycle/permission/business category": use **Option B**, but plan it as a v2-scoped change across types + config + hook + UI + migration + tests.
