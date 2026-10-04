# Mexa Bots

منصة لإنشاء **زملاء عمل بالذكاء الاصطناعي**: بوتات دائمة لكلٍّ منها اسم ووظيفة وذاكرة، تعمل على كمبيوتر سحابي حقيقي (متصفح وطرفية وملفات)، وتنفّذ مهام فعلية وتستمر بعد إغلاق جهازك. تتابعها من **Android وiPhone وسطح المكتب (macOS/Windows/Linux) والويب**.

> **EN:** Mexa Bots is an Arabic-first platform for persistent AI teammates that work on a real cloud computer (browser, terminal, files). One TypeScript monorepo: API, worker, web, desktop (Electron), and mobile (Expo, Android + iOS). Inspired by Grok Bot and Rakazo.

**الحالة:** 🧱 مرحلة التخطيط والـ scaffold. لم يُكتب كود تطبيقي بعد. انظر [docs/STATUS.md](./docs/STATUS.md).

## المحتويات

| المسار | الوصف |
|---|---|
| `apps/api` | خادم Hono + oRPC + WebSocket |
| `apps/worker` | وظائف الخلفية: Runs وRoutines والتلخيص |
| `apps/web` | تطبيق الويب (React 19 + Vite) |
| `apps/desktop` | تطبيق Electron |
| `apps/mobile` | تطبيق Expo لـ Android وiOS |
| `apps/gateway` | بوابة قنوات المراسلة (المرحلة 4) |
| `apps/site` | الموقع التسويقي والتوثيق (Astro) |
| `packages/*` | 15 حزمة مشتركة: contracts وdomain وagent وsandbox وconnectors وdb وauth وapi-client وapp-core وui وui-native وdesign-tokens وi18n وconfig وtest-utils |
| `infra/` | Docker Compose وDockerfiles وصورة الكمبيوتر والنشر |
| `docs/` | الخطة الكاملة وقرارات المعمارية |

## الوثائق

- [الخطة الكاملة](./docs/plan/README.md): الرؤية والمعمارية والميزات والبيانات والـ API والوكيل والكمبيوتر والموبايل والديسكتوب والأمان والنشر وخارطة الطريق.
- [قرارات المعمارية](./docs/adr/README.md)
- [حالة الإنجاز](./docs/STATUS.md)
- [المساهمة](./CONTRIBUTING.md) · [الأمان](./SECURITY.md)

## البدء السريع (بعد اكتمال المرحلة 0)

```bash
nvm use                 # Node 22
corepack enable         # pnpm 10
pnpm install
cp .env.example .env
pnpm infra:up           # Postgres + Redis + MinIO + Mailpit
pnpm db:migrate
pnpm dev
```

## خارطة الطريق

| المرحلة | الأسابيع | المحتوى |
|---|---|---|
| 0 | 1–2 | الأساس |
| 1 | 3–8 | MVP: بوتات ومحادثة |
| 2 | 9–16 | الكمبيوتر والشاشة الحيّة والـ Takeover |
| 3 | 17–22 | Routines والموصلات |
| 4 | 23–30 | الفِرق والفوترة والقنوات وإطلاق المتاجر |
| 5 | 31+ | التوسّع والمؤسسات |

التفاصيل في [17 — خارطة الطريق](./docs/plan/17-roadmap.md).
