# 04 — الحزمة التقنية

الغرض: تثبيت كل اختيار تقني مع بديله وسببه.

## الجدول

| الطبقة | الاختيار | البديل | السبب |
|---|---|---|---|
| اللغة | TypeScript 5.9 | Go/Rust | مشاركة الأنواع مع 3 عملاء ([ADR-0002](../adr/0002-typescript-end-to-end.md)) |
| Monorepo | pnpm + Turborepo | Nx | أبسط وكاش بعيد ([ADR-0001](../adr/0001-monorepo-pnpm-turborepo.md)) |
| API | Hono + oRPC + OpenAPI | NestJS + tRPC | خفيف وعقود zod ([ADR-0005](../adr/0005-hono-orpc-api.md)) |
| Realtime | WebSocket + SSE | Socket.IO | معيار ويعمل على RN |
| قاعدة البيانات | PostgreSQL 16 + pgvector | Mongo | علاقات وبحث متجهي معاً |
| ORM | Prisma 6 | Drizzle | نضج وmigrations ([ADR-0006](../adr/0006-postgres-prisma-graphile-worker.md)) |
| الوظائف | Graphile Worker | BullMQ | داخل Postgres وcron مدمج |
| المصادقة | Better Auth | Clerk | ذاتي الاستضافة، منظمات، Passkeys |
| التخزين | S3-compatible (MinIO/R2) | GridFS | رفع مباشر موقّع |
| النماذج | Vercel AI SDK v5 | LangChain | بث وأدوات ومزوّدون ([ADR-0008](../adr/0008-ai-sdk-model-router-grok-default.md)) |
| الافتراضي | Grok 4.3 وGrok 4.5 | — | سعر وسياق |
| Embeddings | text-embedding-3-small أو bge-m3 | — | يدعم العربية |
| الويب | React 19 + Vite 7 + Tailwind 4 + shadcn/ui | Next.js | SPA قابل لإعادة الاستخدام في Electron |
| حالة العميل | TanStack Query + Zustand | Redux | مشترك مع الموبايل |
| سطح المكتب | Electron 33+ | Tauri 2 | نضج وتحديث تلقائي ([ADR-0004](../adr/0004-electron-for-desktop.md)) |
| الموبايل | Expo + Expo Router + EAS | Flutter | كود وأنواع مشتركة ([ADR-0003](../adr/0003-expo-for-mobile.md)) |
| تنسيق RN | NativeWind + Reanimated 3 | Tamagui | نفس tokens |
| i18n | i18next | Lingui | RTL وplural عربي ([ADR-0010](../adr/0010-arabic-first-rtl.md)) |
| الكمبيوتر | Docker Ubuntu 24.04 | Firecracker | أبسط للبدء ([ADR-0007](../adr/0007-docker-computers-provider-abstraction.md)) |
| الموصلات | MCP + OAuth مباشر | Zapier | معيار مفتوح ([ADR-0009](../adr/0009-mcp-primary-connector-protocol.md)) |
| الصوت | Deepgram/Whisper وElevenLabs/OpenAI | — | أصوات عربية |
| الإشعارات | Expo Push وWeb Push وResend | OneSignal | مندمج مع Expo |
| الدفع | Stripe + RevenueCat | Paddle | امتثال المتاجر |
| المراقبة | OpenTelemetry وSentry وPostHog | Datadog | مفتوح |
| CI/CD | GitHub Actions وEAS وelectron-builder وGHCR | — | |
| الموقع | Astro | Next.js | ثابت وسريع |

## نماذج الذكاء

أسعار xAI (أكتوبر 2026، تُراجَع قبل الإطلاق من docs.x.ai):

| النموذج | إدخال/مليون | إخراج/مليون | السياق | الاستخدام |
|---|---|---|---|---|
| Grok 4.5 | $2.00 | $6.00 | 500K | `powerful` |
| Grok 4.3 | $1.25 ($0.20 مخبّأ) | $2.50 | 1M | `balanced` (الافتراضي) |
| Grok 4.20 | أقل | أقل | — | `fast` |

بعد 200K token من السياق تتضاعف الأسعار، لذلك نلخّص المحادثات الطويلة ونحدّ السياق.

قواعد `packages/agent/model-router`:
- كل بوت له `modelPreset` من `fast | balanced | powerful | custom`.
- Fallback تلقائي عند 429/5xx إلى المزوّد التالي.
- Prompt caching للـ system prompt الطويل.
- ميزانية لكل Run ولكل Routine (tokens ودقائق كمبيوتر).

## الإصدارات المبدئية في `catalog:`

node 22 LTS، pnpm 10، typescript ^5.9، react ^19.1، vite ^7، tailwindcss ^4، hono ^4، @orpc/* ^1، prisma ^6، graphile-worker ^0.16، better-auth ^1.3، ai ^5، @ai-sdk/xai ^2، electron ^33، zod ^4، وexpo بآخر SDK مستقر. تُتحقق قبل أول `pnpm install`.

## قرارات مفتوحة

- Drizzle بدل Prisma إن ظهرت مشاكل أداء: **التوصية** Prisma الآن.
- Bun للـ API: **التوصية** Node 22 للاستقرار.
