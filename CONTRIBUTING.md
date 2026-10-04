# المساهمة في Mexa Bots

## الإعداد

```bash
nvm use && corepack enable
pnpm install
cp .env.example .env
pnpm infra:up
```

المتطلبات: Node 22.12+ وpnpm 10 وDocker.

## سير العمل

1. فرع قصير العمر: `feat/…` أو `fix/…` أو `chore/…`.
2. Conventional Commits: `feat(api): add bots.create`.
3. قبل الـ PR: `pnpm lint && pnpm typecheck && pnpm test`.
4. أضف changeset عند تغيير حزمة: `pnpm changeset`.
5. PR صغير مع قالب الـ PR، ومراجع واحد على الأقل. الدمج بـ squash.

## القواعد

- الحزم لا تستورد من `apps/*`. `contracts` لا تعتمد إلا على `zod`.
- الكود والتعليقات والـ commits بالإنجليزية، والوثائق بالعربية.
- كل مكوّن واجهة يُراجَع بـ RTL (العربية) وLTR وبالوضعين الفاتح والداكن.
- التغيير المعماري الكبير يحتاج ADR في `docs/adr`.
- لا أسرار في الكود أو الـ commits.

راجع [18 — الفريق وأسلوب العمل](./docs/plan/18-team-workflow.md).
