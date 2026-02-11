# MinIO

S3 호환 오브젝트 스토리지. Helm chart로 배포합니다.

## 사전 준비

```bash
helm repo add minio https://charts.min.io/
helm repo update
```

## 배포

```bash
# Local (Mac Docker Desktop)
helm install minio minio/minio -n data-platform --create-namespace \
  -f helm/minio/phase/local/values.yaml \
  -f helm/minio/phase/local/values-secret.yaml
```

## 삭제

```bash
helm uninstall minio -n data-platform
kubectl delete namespace data-platform
```

## 접속

- API: http://localhost:9000
- Console: http://localhost:9001
- 계정: values-secret.yaml 참조
