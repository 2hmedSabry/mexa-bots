# infra/docker

Dockerfiles لخدمات المنتج (تُكتب في المرحلة 0–1):

- `api.Dockerfile` — multi-stage (pnpm fetch → build → node:22-alpine runtime).
- `worker.Dockerfile` — نفس القاعدة مع نقطة دخول `@mexa/worker`.
- `web.Dockerfile` — بناء Vite ثم تقديم ثابت عبر Caddy/nginx.
- `gateway.Dockerfile` — بوابة قنوات المراسلة (المرحلة 4).

تُنشر الصور إلى `ghcr.io/mexa/*` بوسم `edge` (كل دفعة على `main`) و `vX.Y.Z` (الإصدارات). دعم `linux/amd64` و `linux/arm64`.
