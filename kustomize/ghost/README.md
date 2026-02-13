# Ghost Blog

Ghost 블로그를 Kustomize로 배포합니다 (SQLite 사용).

## 구조

```
kustomize/ghost/
├── base/
│   ├── kustomization.yaml
│   ├── deployment.yaml       # Deployment + PVC (ghost:6-alpine)
│   └── service.yaml          # ClusterIP Service
└── overlays/
    └── sandbox/
        ├── kustomization.yaml
        ├── patch-deployment.yaml   # URL(blog.revelly.io), 리소스 설정
        ├── patch-pvc.yaml          # storageClass: local-path
        └── ingress.yaml            # blog.revelly.io → ghost:2368
```

## 배포

```bash
kubectl kustomize kustomize/ghost/overlays/sandbox | ssh sandbox001 "kubectl apply -f -"
```

## 접속

- 블로그: https://blog.revelly.io
- Admin: https://blog.revelly.io/ghost

## 참고

- DB: SQLite (`/var/lib/ghost/content/data/ghost.db`)
- 콘텐츠(글, 이미지, DB)는 PVC `ghost-content` (local-path, 5Gi)에 저장
- Ghost URL을 `http://`로 설정해야 Cloudflare Tunnel 환경에서 리다이렉트 루프 방지
