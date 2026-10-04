# 16 — الاختبار والجودة

الغرض: استراتيجية الاختبار وبوابات الجودة.

## الهرم

| المستوى | الأداة | النطاق |
|---|---|---|
| وحدات | Vitest | `domain` و`agent` مع mocks و`connectors` و`i18n`، تغطية 80% للمنطق الأساسي |
| تكامل | Vitest + Testcontainers (Postgres) | Routers oRPC وPrisma وGraphile وOutbox |
| عقود | zod + snapshot OpenAPI | كسر العقد يفشل CI |
| E2E ويب وديسكتوب | Playwright (وElectron driver) | تسجيل، إنشاء بوت، شات بنموذج وهمي، موافقة، شاشة وهمية |
| E2E موبايل | Maestro | تسجيل، شات، إشعار ثم موافقة، share intent |
| بصري | Playwright screenshots وStorybook | كل مكوّن `ar` و`en`، فاتح وداكن |
| Evals | `packages/agent/evals` | جودة الوكيل ومقاومة injection (أسبوعياً) |
| حِمل | k6 | WS fan-out، 500 Run متزامن، 100 بث شاشة |
| أمان | Semgrep وgitleaks وTrivy وZAP baseline | أسبوعياً وقبل الإصدار |

## المبادئ

- `@mexa/test-utils` يوفّر `MockModelProvider` و`MockComputerProvider` وموصلات وهمية، فلا شبكة حقيقية في CI.
- Factories لكل كيان (`makeBot` و`makeRun`) بدل fixtures ثابتة.
- اختبارات التفويض إلزامية: كل إجراء يُجرَّب كـ owner وmember وغريب.
- اختبار RTL جزء من تعريف الاكتمال لأي مكوّن واجهة.
- الاختبار المتذبذب عيب: لا إعادة تشغيل تلقائي دون تذكرة.

## بوابات الجودة

- **PR:** format وlint وtypecheck وunit وintegration وbuild، ولا تنخفض التغطية.
- **ليلياً:** E2E ويب وموبايل على preview مع evals.
- **قبل الإصدار:** E2E كامل، حِمل، فحص أمني، واختبار يدوي على جهاز Android وiPhone حقيقيين (RTL، إشعارات، Takeover).

## قرارات مفتوحة

- خدمة أجهزة حقيقية (BrowserStack أو Firebase Test Lab): **التوصية** المرحلة 4.
