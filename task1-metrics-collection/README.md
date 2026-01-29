# Task 1: Kubernetes Metrik Toplama

## Görev
Kubernetes üzerinde çalışan pod ve nodeların metriklerini merkezi bir yere toplayıp, 90 gün boyunca Object Storage'da saklamak.

## Mimarisi
- **Prometheus**: Pod/node metriklerini topla (retention: 90 gün)
- **MinIO**: Object Storage (metrics bucket'ında sakla)

## Deployment
```bash
cd metrics-stack
helm dependency update .
helm install metrics-release . -n metrics -f values.yaml
```

## Erişim
- Prometheus: http://localhost:9090
- MinIO: http://localhost:9001 (admin/minio123)

## Doğrulama
- Prometheus → Status → Targets (metrikleri toplandığını gör)
- MinIO → metrics bucket (verilerin saklandığını gör)
