---
name: pwa-optimization
description: Optimize Progressive Web App service worker strategies, precache configuration, offline UX, and manifest settings. Use when tuning PWA performance, reducing precache size, improving offline experience, or fixing service worker caching issues.
---

# PWA Optimization

Service worker tuning, precache strategy optimization, and offline UX patterns for Progressive Web Apps.

## Use this skill when

- Reducing PWA precache bundle size
- Fixing service worker caching issues (stale content, missing assets)
- Improving offline user experience
- Optimizing install prompt timing
- Tuning Workbox runtime caching strategies
- Migrating between `vite-plugin-pwa`, `next-pwa`, or custom service workers

## Do not use this skill when

- Building a native mobile app (use React Native, Flutter, etc.)
- You need general caching strategy without PWA context
- The app is server-rendered without client-side navigation

## Instructions

### Phase 1: Audit Current Configuration

1. Locate the PWA configuration:
   - `vite.config.ts` → `VitePWA()` plugin options
   - `next.config.js` → `next-pwa` configuration
   - Custom `sw.js` or `service-worker.ts`

2. Check manifest.json / webmanifest:
   - [ ] `name` and `short_name` are set and consistent
   - [ ] `display` mode is appropriate (`standalone` for apps, `minimal-ui` for marketing sites)
   - [ ] Icon set includes: 192×192, 512×512, maskable variants
   - [ ] `theme_color` matches the site's primary color
   - [ ] `start_url` and `scope` are correct

3. Measure current precache size:

```bash
# After build, check the workbox precache manifest
cat dist/sw.js | grep -o '"url":"[^"]*"' | wc -l
# Or check total precache size
du -sh dist/
```

### Phase 2: Optimize Precache

4. **Reduce precache scope** — Only precache the app shell:

For `vite-plugin-pwa`:

```ts
workbox: {
  globPatterns: ['**/*.{js,css,html}'],
  // Exclude large vendor chunks, images, and fonts from precache
  globIgnores: [
    '**/vendor-*.js',     // Large vendor chunks
    '**/*.{png,jpg,webp,svg,gif}', // Images (use runtime cache)
    '**/*.{woff,woff2}',  // Fonts (use runtime cache)
  ],
  // Lower the max file size for precache
  maximumFileSizeToCacheInBytes: 500 * 1024, // 500KB, not 5MB
}
```

5. **Move large assets to runtime caching:**

```ts
runtimeCaching: [
  // Images — cache first, long expiry
  {
    urlPattern: /\.(?:png|jpg|jpeg|svg|gif|webp|avif)$/,
    handler: "CacheFirst",
    options: {
      cacheName: "images-cache",
      expiration: { maxEntries: 100, maxAgeSeconds: 30 * 24 * 60 * 60 },
    },
  },
  // Fonts — cache first, very long expiry
  {
    urlPattern: /\.(?:woff|woff2|ttf|otf)$/,
    handler: "CacheFirst",
    options: {
      cacheName: "fonts-cache",
      expiration: { maxAgeSeconds: 365 * 24 * 60 * 60 },
    },
  },
  // API calls — network first with fallback
  {
    urlPattern: /\/api\//,
    handler: "NetworkFirst",
    options: {
      cacheName: "api-cache",
      expiration: { maxAgeSeconds: 5 * 60 },
      networkTimeoutSeconds: 10,
    },
  },
];
```

### Phase 3: Offline UX

6. Design offline fallback experience:
   - Create an `offline.html` page with brand styling
   - Show cached content when available
   - Display clear messaging: "You're offline. Some content may be outdated."
   - Provide a retry button for network-dependent actions

7. Handle offline form submissions:
   - Queue form data in IndexedDB
   - Sync when connectivity returns (Background Sync API)
   - Show user feedback: "Your submission will be sent when you're back online"

### Phase 4: Update Strategy

8. Choose an update strategy:

| Strategy      | Behavior                                       | Best For                |
| ------------- | ---------------------------------------------- | ----------------------- |
| `autoUpdate`  | Silent update, new version loads on next visit | Marketing sites         |
| `prompt`      | Show update banner, user clicks to refresh     | Apps with state         |
| `skipWaiting` | Force immediate update                         | Critical security fixes |

9. For marketing websites, `autoUpdate` is recommended:

```ts
registerType: "autoUpdate";
```

### Phase 5: Verify

10. Test PWA in Chrome DevTools:
    - Application → Service Workers → verify registration
    - Application → Manifest → verify icon and display mode
    - Network → check "Offline" → verify offline experience
    - Application → Cache Storage → verify cache sizes

11. Run Lighthouse PWA audit:

```bash
npx lighthouse <url> --only-categories=pwa
```

## Safety

- Always test service worker changes in incognito/private browsing first
- A broken service worker can cache bad content permanently
- Include a "kill switch" mechanism to force-unregister the SW if needed
- Be careful with `skipWaiting` — it can break in-flight requests
