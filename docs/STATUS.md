# حالة المشروع (STATUS)

آخر تحديث: 2026-10-04 · الفرع: `claude/zen-hypatia-jy78qz`

## ما تم إنجازه

### 1) البحث في المرجعين
- **Grok Bot** (xAI + Cursor، beta منذ أغسطس 2026): البوت، الكمبيوتر السحابي المشترك، Routines، Skills، الموصلات، الموبايل (iOS 18+ / Android 9+)، الـ Takeover، الحدود (50 بوتاً، 50 Routine/بوت، آخر 20 تشغيلاً).
- **Rakazo** (Apache-2.0): بنية monorepo (apps/packages/infra/docs)، Hono + oRPC، Prisma، Better Auth، Graphile Worker، مزوّدو sandbox، Electron وExpo.
- **أسعار Grok API** (أكتوبر 2026): Grok 4.5 بسعر 2/6 دولار للمليون، وGrok 4.3 بسعر 1.25/2.50 دولار.
- الخلاصة موثّقة في [01 — الرؤية](./plan/01-vision.md) و[00 — القرارات](./plan/00-decisions-brief.md).

### 2) هيكل الـ monorepo (جاهز على القرص)
- ملفات الجذر: `package.json`، `pnpm-workspace.yaml`، `turbo.json`، `tsconfig.base.json`، `.nvmrc`، `.npmrc`، `.editorconfig`، `.prettierrc`، `.gitignore`، `.env.example`، Changesets.
- **7 تطبيقات** في `apps/`: api، worker، web، desktop، mobile، gateway، site.
- **15 حزمة** في `packages/`: contracts، domain، agent، sandbox، connectors، db، auth، api-client، app-core، ui، ui-native، design-tokens، i18n، config، test-utils.
- كل workspace فيه `package.json` (`@mexa/*`) و`README.md` عربي/إنجليزي و`src/`.
- حالة الكل: 🧱 Scaffold. السكربتات الحالية placeholders ولم يُكتب أي كود تطبيقي بعد.

### 3) البنية التحتية
- `infra/compose/docker-compose.dev.yml`: Postgres+pgvector، Redis، MinIO، Mailpit.
- `infra/compose/docker-compose.prod.yml`: caddy، api، worker، web، postgres، redis.
- `infra/deploy/caddy/Caddyfile`، وأدلة `infra/docker` و`infra/computer-image` (مواصفات فقط).
- `.github`: workflow للـ CI، قوالب PR وissues، CODEOWNERS، dependabot.

### 4) وثائق الخطة (`docs/plan`)
انظر [الفهرس](./plan/README.md). كل وثيقة تتبع [ملخص القرارات](./plan/00-decisions-brief.md).

## ما لم يتم بعد
- لم يُنفَّذ أي كود تطبيقي، ولم يُشغَّل `pnpm install` ولا أي فحص CI.
- لم تُكتب Dockerfiles ولا صورة الكمبيوتر ولا `install.sh`.
- لم يُنشأ Prisma schema الفعلي.
- لم يُفتح PR (لم يُطلب).

## الخطوة التالية
المرحلة 0 (الأسبوعان 1–2) من [خارطة الطريق](./plan/17-roadmap.md): أدوات الـ monorepo، CI شغّال، Prisma schema v1، Better Auth، design tokens، وتشغيل قشرات web وExpo وElectron مع تسجيل الدخول.
