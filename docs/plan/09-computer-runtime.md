# 09 — كمبيوتر البوت

الغرض: تحديد صورة الكمبيوتر وواجهة `agentd` وتجريد المزوّد ودورة الحياة والـ Takeover.

## الفكرة

لكل منظمة كمبيوتر Linux دائم تشترك فيه بوتاتها: ملفات وملف متصفح وأدوات سطر أوامر. لكل بوت شاشته، لكن الحد الأمني واحد. الكمبيوترات الخاصة لكل بوت تأتي في المرحلة 4.

## الصورة (`infra/computer-image`)

| المكوّن | الاختيار |
|---|---|
| القاعدة | Ubuntu 24.04 (amd64 وarm64) |
| العرض | Xvfb 1280×800 مع XFCE أو Openbox |
| البث | x11vnc + noVNC/websockify (المرحلة 2)، ثم WebRTC (المرحلة 4) |
| المتصفح | Chromium + Playwright، CDP على `127.0.0.1:9222` فقط |
| الأدوات | git, curl, jq, ripgrep, python3, node 22, pandoc, imagemagick, xdotool, scrot |
| التحكم | `agentd` على `127.0.0.1:7777` |
| العمليات | s6-overlay |
| المستخدم | `bot` غير root، بلا sudo، و`/workspace` وملف Chromium على volumes دائمة |

## واجهة agentd

```
POST /exec        { cmd, cwd?, env?, timeoutMs, stdin? } → stream
GET  /fs/list | /fs/read     PUT /fs/write     POST /fs/upload   GET /fs/download
GET  /screenshot  ?scale=0.5 → PNG
POST /input       { type: click|dblclick|type|key|scroll|move }
POST /browser/*   navigate, snapshot, click(ref), fill, extract, pdf
GET  /health      WS /events (خروج عمليات، اكتمال تنزيل)
```

المصادقة بين الخدمات بتوكن لكل كمبيوتر يُمرَّر كمتغير بيئة، وmTLS في المرحلة 5.

## تجريد المزوّد (`packages/sandbox`)

```ts
interface ComputerProvider {
  create(spec: ComputerSpec): Promise<ComputerRef>;
  start(ref): Promise<void>;
  stop(ref): Promise<void>;
  destroy(ref): Promise<void>;
  status(ref): Promise<ComputerStatus>;
  connect(ref): Promise<AgentdClient>;
  screenEndpoint(ref): Promise<ScreenEndpoint>;
  snapshot?(ref): Promise<SnapshotRef>;
  restore?(ref, snap: SnapshotRef): Promise<void>;
}
```

| المزوّد | المرحلة | الاستخدام |
|---|---|---|
| `local-docker` | 2 | تطوير، استضافة ذاتية، وضع الديسكتوب المحلي |
| `docker-remote` | 3 | أسطول VPS لـ Mexa Cloud |
| `e2b` وَ`daytona` | 4 | توسّع مرن |
| `fly-machines` | 4/5 | VMs قريبة من المستخدم |

## دورة الحياة

- **بدء كسول:** عند أول أداة تحتاجه، والهدف 10 ثوانٍ أو أقل.
- **إيقاف عند الخمول:** بعد 15 دقيقة بلا Runs أو مشاهدة؛ الـ Routines تشغّله تلقائياً.
- **الاستعادة:** عند تعطّل الحاوية تُنشأ من جديد على نفس الـ volumes، وما خارج `/workspace` يُفقد.
- **Snapshots** يومية للـ volumes في المرحلة 4.

حالات الكمبيوتر: `stopped`, `starting`, `running`, `idle`, `error`.

## الشاشة والـ Takeover

1. العميل يطلب تذكرة فيفتح الـ API WS proxy إلى websockify.
2. أثناء المشاهدة الإدخال معطّل.
3. `takeover.start` يوقف الـ Run (`paused_for_takeover`) ويمنح العميل الإدخال ويسجّل `ComputerSession`.
4. `takeover.end` يرسل للبوت ملاحظة بأن المستخدم تدخّل ويستأنف.
5. على الموبايل: عرض مناسب للشاشة، نقر وسحب وتكبير، لوحة مفاتيح النظام، وزر "إعادة التحكم للبوت".

## الموارد

| الخطة | CPU | RAM | القرص |
|---|---|---|---|
| Free | 1 vCPU | 2 GB | 5 GB (10 ساعات/شهر) |
| Pro | 2 vCPU | 4 GB | 20 GB |
| Team | 4 vCPU | 8 GB | 50 GB لكل كمبيوتر |

## العزل

`--cpus` و`--memory` و`--pids-limit`، نظام ملفات للقراءة فقط مع tmpfs، `no-new-privileges`، seccomp افتراضي، بلا `CAP_SYS_ADMIN`. شبكة Docker منفصلة بلا وصول للخدمات الداخلية. gVisor (`runsc`) اختياري في Mexa Cloud.

## قرارات مفتوحة

- XFCE أم Openbox: **التوصية** Openbox لخفة الوزن، وXFCE إن لزم تطبيقات مكتبية.
- LibreOffice في الصورة الأساسية: **التوصية** صورة `computer:full` منفصلة.
