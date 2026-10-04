# 08 — وقت تشغيل الوكيل

الغرض: وصف حلقة الوكيل وأدواته وموجّه النماذج في `packages/agent`.

## الحلقة

```
load context → build system prompt → streamText(tools)
  ├─ text delta  → run.delta
  ├─ tool call   → approval gate → execute → run.step → نتيجة للنموذج
  ├─ ask_user    → paused_for_approval → انتظار حتى 24 ساعة
  └─ finish      → write memory → UsageLedger → run.status=completed
```

الحدود: 40 خطوة، ميزانية tokens، ميزانية ثواني كمبيوتر، 30 دقيقة للرسالة و2 ساعة للـ Routine. يعمل داخل `apps/worker`، وكل Run قابل للإلغاء (AbortSignal) والاستئناف من آخر خطوة محفوظة.

## تجميع السياق

يُرتَّب الـ system prompt بحيث تُخبَّأ الأجزاء المستقرة:

1. هوية Mexa وقواعد السلامة الثابتة.
2. تعليمات البوت.
3. ذاكرة البوت: الملف الشخصي، أهم الحقائق، الملخصات، استرجاع دلالي.
4. حالة الكمبيوتر ومحتوى `/workspace` المختصر.
5. الموصلات المتاحة وتسلسل الأفضلية.
6. الـ Skills ذات الصلة.
7. تعليمات اللغة: أجب بلغة المستخدم وبتفضيله للأرقام والتواريخ.
8. آخر N رسالة مع تلخيص ما قبلها.

## الأدوات

| المجموعة | الأدوات |
|---|---|
| `computer` | exec, screenshot, click, type, key, scroll |
| `files` | list, read, write, move, delete, upload_to_chat |
| `browser` | navigate, snapshot, click(ref), fill, extract, download, wait |
| `connectors` | تُولَّد ديناميكياً من MCP والموصلات الأولى |
| `memory` | remember, forget, recall |
| `delegate` | to_bot, spawn_subagent |
| `user` | ask, request_approval, send_draft |
| `routine` | propose |
| `skill` | save_from_current_run, run |

لكل أداة مخطط zod وتصنيف خطورة `safe | sensitive | destructive`. الأفعال destructive والإرسال الخارجي تطلب موافقة افتراضياً. المتصفح يعيد snapshot نصياً من شجرة الوصول لأنه أرخص من الصور.

## موجّه النماذج

```ts
type ModelPreset = 'fast' | 'balanced' | 'powerful' | 'custom';
// balanced → xai:grok-4.3 → anthropic → openai → openrouter
// powerful → xai:grok-4.5 → ...
// fast     → xai:grok-4.20 → ...
```

- المفاتيح: مفاتيح Mexa للخطط المدفوعة أو BYO لكل منظمة في `Credential`.
- Ollama للتشغيل الذاتي الكامل، وOpenRouter كمزوّد جامع.
- الاستخدام من استجابة النموذج يُكتب في `UsageLedger` بجدول أسعار قابل للتحديث.

## الذاكرة

- بعد كل Run يستخرج نموذج `fast` الحقائق والتفضيلات ويزيل التكرار عند cosine فوق 0.92.
- Worker ليلي يلخّص المحادثات الطويلة إلى `Memory(kind=summary)`.
- المستخدم يرى ويحرر ويحذف كل شيء.

## الدفاع من Prompt Injection

- ما يعود من المتصفح والملفات والموصلات يُغلَّف كبيانات غير موثوقة مع تعليمة بعدم اتباع أوامره.
- الأفعال sensitive وdestructive تمر بالموافقة مهما قال المحتوى الخارجي.
- كاشف أسرار (مفاتيح API وأرقام بطاقات) يمنع الإرسال الخارجي ويُقنِّع السجلات.
- allow-list للمجالات لكل Routine اختيارياً.

## التقييم

سيناريوهات في `packages/agent/evals` بالعربية والإنجليزية: فرز بريد، استخراج من موقع، تقرير، احترام الموافقات، مقاومة injection. تعمل أسبوعياً في CI على `MockComputerProvider`، وعلى نموذج حقيقي يدوياً قبل كل إصدار. المقاييس: نجاح المهمة، عدد الخطوات، التكلفة، الموافقات غير الضرورية.

## قرارات مفتوحة

- Runs قصيرة داخل الـ API لخفض TTFT: **التوصية** الـ Worker دائماً في المرحلة 1.
- computer-use الأصلي بلقطات: **التوصية** snapshot نصي أولاً ولقطات عند الفشل.
