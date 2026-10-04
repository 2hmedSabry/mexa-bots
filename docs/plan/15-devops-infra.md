# 15 — DevOps والبنية التحتية

الغرض: البيئات وخطوط CI/CD والنشر والمراقبة.

## البيئات

| البيئة | الغرض | العنوان |
|---|---|---|
| local | `pnpm infra:up` ثم `pnpm dev` | localhost |
| preview | لكل PR | `pr-<n>.preview.mexa.app` |
| staging | مطابقة للإنتاج | `staging.mexa.app` |
| production | | `app.mexa.app` و`api.mexa.app` |

## خطوط CI

| Workflow | المحفّز | المحتوى |
|---|---|---|
| `ci.yml` (موجود) | PR وpush على main | format وlint وtypecheck وtest وbuild |
| `e2e-web.yml` | PR بوسم / ليلي | Playwright على preview |
| `docker.yml` | push main / وسم | بناء `api` و`worker` و`web` و`gateway` إلى GHCR، multi-arch، Trivy، cosign |
| `computer-image.yml` | تغيّر `infra/computer-image` | نشر `ghcr.io/mexa/computer` |
| `mobile.yml` | PR: `eas update`؛ وسم `mobile-v*`: build وsubmit | |
| `desktop.yml` | وسم `desktop-v*` | مصفوفة mac/win/linux وتوقيع وRelease |
| `release.yml` | push main | Changesets |
| `security.yml` | أسبوعي | audit وSemgrep وgitleaks |
| `evals.yml` | أسبوعي | تقييم الوكيل على mocks |

## النشر

- **المراحل 1 إلى 3:** VPS (Hetzner) بـ `docker-compose.prod.yml` وCaddy، وPostgres مُدار (Neon/RDS) أو محلي مع نسخ احتياطي، وR2 للتخزين، وكمبيوترات على عقدة مخصّصة عبر `docker-remote`.
- **المراحل 4 و5:** Kubernetes (k3s أو مُدار) مع HPA، وRedis مُدار، وTerraform، وCDN.
- **الاستضافة الذاتية:** `install.sh` يولّد `.env` بأسرار عشوائية ويشغّل compose، والتحديث بـ `docker compose pull && up -d`.

## المراقبة

- OpenTelemetry في API وWorker وagentd يربط Run بالخطوات واستدعاءات النموذج والكمبيوتر، إلى Grafana أو Axiom.
- Sentry للويب والديسكتوب والموبايل والـ API مع source maps من CI. PostHog للتحليلات والأعلام.
- لوحات: TTFT، نجاح Runs، كمون الأدوات، tokens والتكلفة، تشغيل الكمبيوترات، أخطاء الموصلات، صحة WS.
- تنبيهات: أخطاء فوق 2%، تأخر الـ Worker فوق 5 دقائق، كمبيوترات في حالة error، تجاوز ميزانية النماذج اليومية.

## النسخ الاحتياطي

Postgres بـ PITR ونسخة يومية خارج الموقع (RPO 15 دقيقة وRTO ساعة)، وS3 بـ versioning، وsnapshot يومي لـ volumes الكمبيوترات بالاحتفاظ 7 أيام، وتمرين استعادة ربع سنوي.

## تقدير التكلفة الشهرية عند الإطلاق (تقريبي)

| العنصر | التكلفة |
|---|---|
| 2 VPS للـ API وWorker وWeb | ~50$ |
| Postgres مُدار | 25 إلى 50$ |
| عقدة كمبيوترات (16 vCPU/64GB) تخدم ~25 كمبيوتراً متزامناً | ~120$ |
| R2 وCDN | ~10$ |
| Sentry وPostHog وAxiom | 0 إلى 50$ |
| EAS Production | ~99$ |
| Apple Developer وGoogle Play | 99$ سنوياً و25$ مرة |
| النماذج | متغيّر أو BYO |

## قرارات مفتوحة

- Hetzner أم مزوّد ثان لعقد الكمبيوترات: **التوصية** Hetzner، مع مزوّد ثانٍ في المرحلة 4 لتوزيع المخاطر.
