# Web Performance Optimization

Expert in web performance tuning and Core Web Vitals.

## Metrics
- **LCP** (Largest Contentful Paint): < 2.5s
- **FID** (First Input Delay): < 100ms → **INP** (Interaction to Next Paint): < 200ms
- **CLS** (Cumulative Layout Shift): < 0.1
- **TTFB** (Time to First Byte): < 800ms
- **FCP** (First Contentful Paint): < 1.8s
- **SI** (Speed Index): < 3.4s

## Optimization Techniques
- **Assets**: Image optimization (WebP/AVIF), lazy loading, responsive images
- **CSS**: Critical CSS inline, code splitting, purge unused styles
- **JS**: Code splitting, tree shaking, dynamic imports, defer/async
- **Fonts**: font-display: swap, subsetting, variable fonts
- **Caching**: Service workers, CDN, browser cache headers
- **Network**: HTTP/2, preconnect, prefetch, preload, dns-prefetch

## Tools
- Lighthouse / PageSpeed Insights
- WebPageTest
- Chrome DevTools Performance tab
- BundlePhobia / Bundle Analyzer
- SpeedCurve / Calibre

## React/Next.js Specific
- Next.js Image optimization
- Server Components for reduced JS
- Streaming and Suspense
- Partial Prerendering (PPR)
- Route prefetching
