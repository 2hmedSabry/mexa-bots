# خطة مشروع Mexa Bots — الفهرس

الخطة الكاملة لمنصة **Mexa Bots**: زملاء عمل بالذكاء الاصطناعي على كمبيوتر سحابي، عبر Web وDesktop (macOS/Windows/Linux) وMobile (Android + iOS) في monorepo واحد.

المرجعان: [Grok Bot](https://docs.x.ai/grok-bot) لتجربة المنتج، و[Rakazo](https://github.com/elie222/rakazo) للمعمارية مفتوحة المصدر.

## الوثائق

| # | الوثيقة | الحالة |
|---|---|---|
| 00 | [ملخص القرارات](./00-decisions-brief.md) | مكتملة |
| 01 | [الرؤية والتموضع](./01-vision.md) | مكتملة |
| 02 | [المعمارية العامة](./02-architecture.md) | مكتملة |
| 03 | [هيكل الـ Monorepo](./03-monorepo.md) | مكتملة |
| 04 | [الحزمة التقنية](./04-tech-stack.md) | مكتملة |
| 05 | [مواصفات الميزات](./05-features.md) | مكتملة |
| 06 | [نموذج البيانات](./06-data-model.md) | مكتملة |
| 07 | [الـ API والـ Realtime](./07-api-realtime.md) | مكتملة |
| 08 | [وقت تشغيل الوكيل](./08-agent-runtime.md) | مكتملة |
| 09 | [كمبيوتر البوت](./09-computer-runtime.md) | مكتملة |
| 10 | [تطبيق الموبايل](./10-mobile.md) | مكتملة |
| 11 | [تطبيق سطح المكتب](./11-desktop.md) | مكتملة |
| 12 | [الويب ونظام التصميم](./12-web-design-system.md) | مكتملة |
| 13 | [الموصلات](./13-connectors.md) | مكتملة |
| 14 | [الأمان والخصوصية](./14-security.md) | مكتملة |
| 15 | [DevOps والبنية التحتية](./15-devops-infra.md) | مكتملة |
| 16 | [الاختبار والجودة](./16-testing.md) | مكتملة |
| 17 | [خارطة الطريق](./17-roadmap.md) | مكتملة |
| 18 | [الفريق وأسلوب العمل](./18-team-workflow.md) | مكتملة |

قرارات المعمارية (ADRs): [docs/adr](../adr/README.md) — حالة الإنجاز: [docs/STATUS.md](../STATUS.md)

## مفتاح الحالة

🟢 MVP (المرحلة 1) · 🟡 v1 (المراحل 2–4) · 🔵 v2 · 🧱 Scaffold

## القرارات في لمحة

| البُعد | القرار |
|---|---|
| اللغة والأدوات | TypeScript، Node 22، pnpm 10، Turborepo، Changesets |
| API | Hono + oRPC (zod) + WebSocket |
| البيانات | PostgreSQL 16 + pgvector، Prisma، Graphile Worker |
| المصادقة | Better Auth |
| الذكاء | Vercel AI SDK؛ Grok 4.3 افتراضياً، Grok 4.5 للمهام المعقدة، BYO لأي مزوّد |
| الويب | React 19 + Vite + Tailwind 4 + shadcn/ui |
| سطح المكتب | Electron (نفس renderer الويب) |
| الموبايل | Expo + Expo Router + EAS (Android 9+ / iOS 16+) |
| الكمبيوتر | Docker (Ubuntu 24.04 + Xvfb + noVNC + Chromium + agentd) |
| الموصلات | MCP أساسي + OAuth مباشر + خزنة AES-256-GCM |

## المصطلحات

| المصطلح | المعنى |
|---|---|
| Bot | زميل عمل بالذكاء الاصطناعي: اسم + لقب + تعليمات + ذاكرة + محادثة طويلة الأمد |
| Computer | بيئة Linux دائمة بمتصفح وطرفية وملفات، مشتركة بين بوتات المنظمة |
| Run | تنفيذ واحد للوكيل؛ يتكوّن من خطوات |
| Routine | مهمة مجدولة أو مُطلَقة بحدث |
| Skill | مسار متعدد الخطوات يمكن إعادة تشغيله |
| Connector | تكامل مع خدمة خارجية (MCP / OAuth) |
| Approval | طلب موافقة بشرية قبل فعل حسّاس |
| Takeover | تحكّم المستخدم بشاشة الكمبيوتر ثم إعادة التحكم للبوت |
| Channel | قناة مراسلة خارجية (Telegram / WhatsApp / Slack) |
