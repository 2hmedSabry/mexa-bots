# 13 — الموصلات والتكاملات

الغرض: تحديد كيف تصل البوتات إلى الخدمات الخارجية في `packages/connectors`.

## تسلسل الوصول

1. موصل مُهيكل (أدوات مُسمّاة وبيانات مُنمَّطة، يُذكر بـ `@`).
2. API أو CLI رسمي داخل الكمبيوتر.
3. المتصفح السحابي مع Takeover للمصادقة.
4. الجهاز المحلي (🔵، بإذن لكل مرة).

## البروتوكولات

| النوع | الوصف | المرحلة |
|---|---|---|
| MCP | عميل Streamable HTTP مع OAuth 2.1 وPKCE؛ تُحوَّل الأدوات إلى أدوات AI SDK | 3 |
| موصلات أولى | OAuth مباشر لجودة وتحكم أعلى بالصلاحيات | 3 و4 |
| Webhooks واردة | لإطلاق Routines بالأحداث | 3 |
| OpenAPI import | رفع مواصفة وتوليد أدوات | v2 |
| Composio / Pipedream | كتالوج مُدار اختياري | v2 |

## الموصلات الأولى

| الخدمة | أدوات مثال | المرحلة |
|---|---|---|
| Gmail | search, read, draft, send (موافقة) | 3 |
| Google Calendar | list, create/update (موافقة) | 3 |
| Drive وDocs وSheets | search, read, create, append | 3 |
| Notion | search, query, create/update page | 3 |
| Slack | read, post (موافقة), DM | 3 |
| GitHub | issues, PRs, comments, actions | 3 |
| Telegram | إرسال واستقبال (قناة وموصل) | 4 |
| X | read, post (موافقة) | 4 |
| Microsoft 365 وWhatsApp وStripe وCRMs | | v2 |

## خزنة الاعتماد

- AES-256-GCM بمفتاح `VAULT_MASTER_KEY` (أو KMS في Mexa Cloud) مع `keyVersion` للتدوير.
- الـ Worker يجدّد التوكنات قبل انتهائها.
- حقول آمنة في الواجهة: القيمة مُقنَّعة ولا تصل للنموذج ولا لـ `RunStep.output`.
- كاشف مفاتيح في الشات يحذّر ويقترح الحفظ في الخزنة.

## الحسابات والصلاحيات

- `Connector.accountLabel` يميّز الحسابات (بريد العمل مقابل الشخصي)، ويختار البوت بـ `@gmail:work` أو يسأل.
- نطاقات OAuth أدنى افتراضياً، والرفع يطلب موافقة.
- تصنيف خطورة لكل أداة: read آمن، write حسّاس، delete وsend مدمّر.
- سياسة المنظمة قد تمنع موصلات أو تفرض موافقة على كل كتابة.

## قرارات مفتوحة

- Composio مبكراً لتوسيع الكتالوج: **التوصية** v2 بعد نضج MCP.
