# ADR-0004: Electron لسطح المكتب

- **الحالة:** مقبول
- **التاريخ:** 2026-10-04

## السياق

نحتاج macOS وWindows وLinux، وتشغيلاً محلياً عبر Docker، وإعادة استخدام واجهة الويب.

## القرار

Electron 33+ مع electron-vite وelectron-builder وelectron-updater. الـ renderer هو `apps/web`.

## العواقب

- كود واجهة واحد للويب والديسكتوب، وتحديث تلقائي ناضج.
- حجم أكبر من Tauri، ونقبله مقابل النضج وسهولة إدارة Docker من Node.
- أمان صارم إلزامي: contextIsolation وsandbox وIPC مُنمَّط.
