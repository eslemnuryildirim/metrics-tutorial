# Task 1: Kubernetes Metrik Toplama

Görev: Kubernetes üzerinde çalışan pod ve nodeların metrikleri merkezi bir yere toplayınız.
Ödev Detayı: Metrikler 90 gün, Object Storage üzerinde saklanacak
Soru: Object storage nedir? On-prem ve cloud için örnek verir misiniz?

## Object Storage Nedir?
Verileri objeler halinde saklayan depolama türü.

Cloud: AWS S3, Google Cloud Storage, Azure Blob
On-Premise: MinIO, Ceph, SeaweedFS

## Yapılan İşlemler

### 1. Proje Yapısı
```bash
mkdir -p metrics-tutorial/task1-metrics-collection
cd metrics-tutorial/task1-metrics-collection
helm create metrics-stack
```

### 2. Chart.yaml (Dependencies)
```yaml
dependencies:
  - name: prometheus
    version: "25.0.0"
  - name: minio
    version: "5.0.0"
```

### 3. values.yaml (Konfigürasyon)
```yaml
prometheus:
  prometheusSpec:
    retention: 90d
    retentionSize: "2GB"

minio:
  rootUser: admin
  rootPassword: minio123
  persistence:
    size: 1Gi
  buckets:
    - name: metrics
```

### 4. Kurulum
```bash
kubectl create namespace metrics
helm dependency update ./metrics-stack
helm install metrics-release ./metrics-stack -n metrics
```

![Namespace Create](task1-metrics-collection/screenshots/namespacecreate.png)
![Helm Dependency Update](task1-metrics-collection/screenshots/helmdependecyupdate.png)
![Helm Lint](task1-metrics-collection/screenshots/helmlint.png)
![Helm Install](task1-metrics-collection/screenshots/helminstall.png)

### 5. Erişim
```bash
ssh vagrant@192.168.56.10
kubectl port-forward -n metrics svc/metrics-release-prometheus-server 9090:80 &
kubectl port-forward -n metrics svc/metrics-release-minio-console 9001:9001 &
```

## Sonuç

Prometheus - http://localhost:9090:
![Prometheus](task1-metrics-collection/screenshots/prometheus.png)

MinIO - http://localhost:9001 (admin/minio123):
![Object Store](task1-metrics-collection/screenshots/object%20store.png)

Kubernetes pod ve node metrikleri toplanıyor.
MinIO object storage'da 90 gün saklanıyor.
Helm ile otomatize edildi.