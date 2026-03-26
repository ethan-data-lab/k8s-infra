# Ghost Blog

Kustomize로 Ghost(SQLite) 배포.

## 구조

```
kustomize/ghost/
├── base/
│   ├── kustomization.yaml
│   ├── deployment.yaml
│   └── service.yaml
├── extras/ghost/settings/routes.yaml   # 형상관리용 원본 → PVC에 수동 반영
└── overlays/sandbox/
    ├── kustomization.yaml
    ├── patch-deployment.yaml
    ├── patch-pvc.yaml
    ├── config.production.json            # → ConfigMap ghost-config
    ├── middleware-ghost.yaml             # Traefik: X-Forwarded-Proto
    ├── ingress.yaml
    ├── smtp.env.example
    └── smtp.env                          # gitignore, 로컬 전용
```

## 배포

```bash
kubectl apply -k kustomize/ghost/overlays/sandbox --context sandbox
```

원격에서만 `kubectl` 쓸 때:

```bash
kubectl kustomize kustomize/ghost/overlays/sandbox | ssh sandbox001 "kubectl apply -f -"
```

(`smtp.env`가 있는 쪽에서 `kustomize`를 실행해야 `ghost-smtp` Secret이 포함된다.)

## SMTP

1. `smtp.env.example` → `smtp.env` 복사 후 `mail__options__auth__pass` 등 입력.
2. `secretGenerator`가 `ghost-smtp` Secret을 만들고, Deployment는 `envFrom`으로 주입한다.
3. Resend 사용 시 `mail__from`은 검증된 도메인과 맞출 것(예: `Blog <noreply@revelly.io>`). Secret 변경 후 `kubectl rollout restart deployment/ghost -n sandbox`.

## 접속

- https://blog.revelly.io
- Admin: https://blog.revelly.io/ghost

## 참고

- **URL / 바인드**: `config.production.json`에 `url`, `server.host`(예: `::`), `paths.contentPath` 등. cloudflared 뒤에서도 공개 URL은 `https://`로 두고, **Traefik Middleware**로 `X-Forwarded-Proto: https`를 넘긴다.
- **PVC**: `ghost-content`(local-path), DB·이미지는 `/var/lib/ghost/content/`.
- **ConfigMap** `config.production.json`은 읽기 전용이라 Ghost가 파일에 쓰는 동작은 실패할 수 있다.
