# infra/computer-image — صورة "كمبيوتر البوت"

الصورة التي يعمل داخلها كل كمبيوتر (sandbox) مخصّص للمستخدم/الفريق. المواصفات الكاملة في
[docs/plan/09-computer-runtime.md](../../docs/plan/09-computer-runtime.md).

**المحتوى المخطط:**

- Ubuntu 24.04 LTS (amd64 + arm64)
- Xvfb + سطح مكتب خفيف (XFCE أو Openbox) + x11vnc + noVNC/websockify
- Chromium + Playwright (CDP مفتوح على loopback فقط)
- أدوات: git, curl, jq, python3, node 22, ripgrep, pandoc, libreoffice (اختياري), xdotool, scrot
- `agentd` — خدمة تحكّم (Node/TypeScript) تعرض HTTP/WS API داخلي: `exec`, `fs`, `screenshot`, `input`, `browser`, `health`
- مستخدم غير-root `bot`، مجلد العمل الدائم `/workspace` (volume)
- Supervisor (s6-overlay) لإدارة العمليات

**الحالة:** 🧱 لم تُبنَ بعد — المرحلة 2 من خارطة الطريق.
