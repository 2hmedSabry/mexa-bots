# ADR-0003: Expo لتطبيق الموبايل

- **الحالة:** مقبول
- **التاريخ:** 2026-10-04

## السياق

نحتاج Android وiOS بتكافؤ ميزات من أول يوم، وبفريق TypeScript واحد.

## القرار

Expo (آخر SDK مستقر) مع Expo Router وEAS Build وSubmit وUpdate، وNativeWind للتنسيق.

## العواقب

- مشاركة `contracts` و`api-client` و`app-core` و`i18n` مع الويب.
- تحديثات OTA لإصلاحات JS.
- الشاشة الحيّة عبر WebView لـ noVNC أولاً ثم react-native-webrtc، وكلاهما يعمل مع Dev Client.
- بديل مرفوض: Flutter (يكسر مشاركة الكود).
