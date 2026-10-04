# 12 — الويب ونظام التصميم

الغرض: مواصفات `apps/web` ونظام التصميم المشترك بين الويب والموبايل.

## تطبيق الويب

- React 19 وVite 7 وTanStack Router وQuery وZustand وTailwind 4 وshadcn/ui (Radix) وLucide.
- SPA على `app.mexa.app` وrenderer داخل Electron بنفس الحزمة (`VITE_TARGET=web|desktop`).
- PWA مع Web Push.
- التخطيط: شريط جانبي (البوتات، الكمبيوتر، الوارد، الـ Routines، الموصلات، الإعدادات) ومنطقة محتوى ولوحة سياق، وكلها تنعكس في RTL.

## الشاشات

| الشاشة | المحتوى |
|---|---|
| البوتات | قائمة، إنشاء من قالب، البوت الأساسي |
| المحادثة | بث، مرفقات، خطوات قابلة للطي، مسودات بأزرار موافقة، شاشة حيّة مصغّرة |
| الكمبيوتر | الشاشة الكاملة وTakeover، متصفح الملفات، الجلسات، الاستخدام |
| الـ Routines | cron builder بالعربية، سياسة الإشعار، آخر 20 تشغيلاً، Test run |
| الموصلات | الكتالوج، الحسابات، إضافة MCP، خزنة الاعتماد |
| الوارد | الموافقات والأسئلة والإشعارات |
| الإعدادات | الحساب، المنظمة، النماذج والمفاتيح، الفوترة، اللغة، الأمان، التدقيق |
| الإدارة (داخلي) | المستخدمون، الكمبيوترات، الأعطال، الأعلام |

## نظام التصميم

الحزم: `@mexa/design-tokens` و`@mexa/ui` و`@mexa/ui-native`. المصدر الواحد للـ tokens يولّد Tailwind preset وRN theme.

- **الألوان:** semantic (`bg`, `surface`, `text`, `muted`, `primary`, `accent`, `success`, `warning`, `danger`) بوضعين فاتح وداكن، وألوان لحالة البوت (يعمل، ينتظر، خامل، خطأ).
- **الخطوط:** IBM Plex Sans Arabic (بديل Noto Sans Arabic) وInter وJetBrains Mono. الحجم 16px وارتفاع السطر 1.7 للعربية.
- **المسافات:** سلم 4px وزوايا 8 و12 و16.
- **الحركة:** 150 و250 و400ms.
- **الأيقونات:** Lucide مع قلب الأيقونات الاتجاهية في RTL.

## قواعد RTL

- خصائص CSS المنطقية فقط (`ms-` و`me-` و`ps-` و`text-start`)، و`dir` على `html` يتبع اللغة.
- الأرقام والتواريخ عبر `Intl` بتفضيل المستخدم.
- فقاعة المستخدم عند البداية والبوت عند النهاية، والكود والطرفية LTR داخل حاوية RTL.
- لقطات Playwright لكل مكوّن بـ `ar` و`en`.

## مكوّنات خاصة بالمنتج

`BotAvatar`, `BotStatusBadge`, `MessageBubble`, `RunSteps`, `ApprovalCard`, `ComputerScreen`, `RoutineScheduleEditor`, `ConnectorCard`, `SecretField`, `UsageMeter`, `EmptyState`.

## إمكانية الوصول

WCAG 2.1 AA: تباين وتنقل بلوحة المفاتيح و`aria-live` للبث. Storybook لـ `@mexa/ui` مع إضافة a11y ومبدّل RTL، وStorybook RN لـ `ui-native` لاحقاً.

## قرارات مفتوحة

- إنشاء الهوية البصرية والشعار: **التوصية** مصمم في المرحلة 0 قبل بناء tokens النهائية.
- TanStack Router أم React Router: **التوصية** TanStack لأنواع المسارات.
