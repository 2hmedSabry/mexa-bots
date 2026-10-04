# 07 — الـ API والـ Realtime

الغرض: تحديد عقود الـ API وبروتوكول الأحداث الحية.

## المبادئ

- oRPC فوق Hono، والمخططات zod في `packages/contracts`، وOpenAPI على `/api/openapi.json`.
- الويب والديسكتوب بكوكي HttpOnly، والموبايل بـ Bearer token من جلسة الجهاز.
- كل كتابة تقبل `Idempotency-Key` وتُخبَّأ نتيجتها 24 ساعة.
- التصفح بـ cursor. أكواد أخطاء ثابتة: `UNAUTHORIZED`, `FORBIDDEN`, `NOT_FOUND`, `RATE_LIMITED`, `PLAN_LIMIT`, `COMPUTER_UNAVAILABLE`.
- Rate limiting لكل مستخدم ومنظمة.

## الموجّهات

| Router | الإجراءات |
|---|---|
| `auth` | Better Auth على `/api/auth/*` |
| `me` | get, update, organizations.list, switchOrganization |
| `organizations` | create, update, members.*, settings.*, usage.summary |
| `bots` | list, get, create, update, delete, setPrimary, templates.list |
| `conversations` | list, get, create, archive, search |
| `messages` | list, send (يعيد messageId وrunId), react, delete |
| `runs` | get, list, cancel, steps.list, retry |
| `memory` | list, create, update, delete, search |
| `routines` | list, get, create, update, toggle, delete, runNow, runs.list |
| `skills` | list, get, createFromRun, update, delete, run |
| `computers` | get, start, stop, restart, screenshot, screen.ticket, takeover.start/end, files.*, usage |
| `connectors` | catalog, list, connect, disconnect, mcp.add/test/remove |
| `credentials` | list (بلا قيم), create, rotate, delete |
| `approvals` | list, get, decide |
| `files` | presignUpload, get, delete |
| `notifications` | list, markRead, preferences.* |
| `devices` | register, unregister |
| `billing` | plans, subscription.get, checkout.create, portal, iap.verify |
| `channels` | telegram.link/unlink (المرحلة 4) |
| `admin` | users, organizations, runs, computers, flags |
| `health` | live, ready, version |

## الـ Realtime

نقطة واحدة `wss://api.mexa.app/ws` بعد المصادقة:

```ts
// client → server
{ type: 'subscribe', topics: ['org:ID', 'conversation:ID', 'run:ID', 'computer:ID'] }
{ type: 'unsubscribe', topics: [] }
{ type: 'ping' }

// server → client
message.created | run.status | run.delta | run.step
approval.requested | approval.decided
computer.status | computer.takeover
routine.run.finished | notification | presence
```

- SSE بديل على `GET /api/runs/:id/stream` للموقع والـ gateway.
- Redis pub/sub بين نسخ الـ API، وEventEmitter داخلي لنسخة واحدة.
- إعادة الاتصال: العميل يرسل `lastEventId` فيُعاد ما فاته من `Outbox` خلال 5 دقائق.
- حد 5 اتصالات WS متزامنة لكل مستخدم.

## بث الشاشة

- المرحلة 2: `/ws/screen/:computerId?ticket=` يمرّر ثنائياً إلى websockify، وnoVNC على الويب وWebView على الموبايل.
- المرحلة 4: `screen.ticket` يعيد SDP/ICE لـ WebRTC، والإدخال عبر DataChannel.

## رفع الملفات

1. `files.presignUpload({ name, mime, size })` يعيد `uploadUrl` و`fileId`.
2. العميل يرفع مباشرة إلى S3/MinIO.
3. `messages.send` بجزء من نوع `file` يحمل `fileId`.

## Webhooks الواردة

`/webhooks/stripe`, `/webhooks/revenuecat`, `/webhooks/telegram/:token`, `/webhooks/whatsapp`, `/webhooks/connectors/:id`. كلها تتحقق من التوقيع وتكتب في `Outbox` وترد 200 فوراً.

## النسخ

العميل الأقدم بإصدارين مدعوم عبر حقول اختيارية فقط. الترويسة `X-Mexa-Client: mobile/1.4.0 (ios)` تفعّل الحد الأدنى للإصدار وشاشة "تحديث مطلوب".

## قرارات مفتوحة

- GraphQL للوحة الإدارة: **التوصية** لا، oRPC يكفي.
