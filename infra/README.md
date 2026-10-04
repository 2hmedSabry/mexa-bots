# infra/

كل ما يتعلق بالبنية التحتية والتشغيل:

| المجلد | الوصف |
|---|---|
| `compose/` | ملفات Docker Compose للتطوير المحلي والاستضافة الذاتية (Postgres + pgvector، Redis، MinIO، Mailpit). |
| `docker/` | Dockerfiles لخدمات المنتج (`api`, `worker`, `web`, `gateway`). |
| `computer-image/` | صورة "كمبيوتر البوت": Ubuntu + Xvfb + سطح مكتب خفيف + noVNC + Chromium + Playwright + `agentd`. |
| `deploy/` | إعدادات النشر: Caddy (HTTPS)، لاحقاً Kubernetes/Terraform. |

راجع [docs/plan/15-devops-infra.md](../docs/plan/15-devops-infra.md) و [docs/plan/09-computer-runtime.md](../docs/plan/09-computer-runtime.md).
