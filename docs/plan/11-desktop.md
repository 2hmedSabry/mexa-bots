# 11 — تطبيق سطح المكتب

الغرض: مواصفات `apps/desktop`. الديسكتوب مركز الإدارة الكامل وبوابة وضع التشغيل المحلي.

## الحزمة

- Electron 33+ مع electron-vite (main وpreload وrenderer).
- الـ renderer هو `apps/web` نفسه، ويكشف preload الكائن `window.mexa` لميزات سطح المكتب.
- electron-builder للتعبئة وelectron-updater للتحديث من GitHub Releases، بقناتين `stable` و`beta`.
- الأمان: `contextIsolation`, `sandbox`, بلا `nodeIntegration`، وCSP صارم، وIPC مُنمَّط بـ zod.
- Sentry للـ main والـ renderer.

## الأنماط الثلاثة

| النمط | السلوك |
|---|---|
| Cloud | يتصل بـ `https://api.mexa.app` |
| Self-hosted | المستخدم يُدخل عنوان خادمه |
| Local | يتحقق من Docker (Desktop/Colima/Podman)، يسحب `ghcr.io/mexa/*`، يشغّل compose مدمجاً (api وworker وpostgres وcomputer) ويتصل به |

في النمط المحلي تبقى البيانات في مجلد بيانات التطبيق. اتصال الموبايل بالنمط المحلي عبر الشبكة المحلية أو tunnel اختياري (🔵).

## الميزات الخاصة

- أيقونة Tray بعدد الموافقات المعلّقة وإشعارات نظام.
- اختصار عام لفتح رسالة سريعة لبوت.
- نوافذ متعددة، نافذة لكل شاشة كمبيوتر.
- Deep links: `mexa://bot/ID` و`mexa://approval/ID`.
- سحب وإفلات الملفات إلى المحادثة.
- 🔵 Route egress عبر الجهاز، وLocal computer access بإذن لكل مرة.

## الأهداف والتوقيع

| المنصة | المعماريات | التوقيع |
|---|---|---|
| macOS | arm64 وx64 | Developer ID + Notarization |
| Windows | x64 وarm64 | Azure Trusted Signing أو EV، مثبّت NSIS |
| Linux | x64 وarm64 | AppImage وdeb وrpm |

## التوزيع

مصفوفة GitHub Actions (macos-14 وwindows-latest وubuntu-latest) عند وسم `desktop-v*`، والمخرجات على GitHub Releases مع `latest.yml`. صفحة التنزيل في `apps/site` تكشف المنصة. لاحقاً: Mac App Store وMicrosoft Store وHomebrew وwinget.

## الأداء

حجم أقل من 150MB، فتح أقل من 1.5 ثانية، تحميل كسول لـ noVNC.

## قرارات مفتوحة

- Flatpak وSnap: **التوصية** بعد الإطلاق إن طُلب.
- ترقية Electron: **التوصية** آخر مستقر كل 8 أسابيع.
