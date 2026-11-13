# Infrastruktura Flux

`clusters/homelab/infrastructure.yaml` synchronizuje ten katalog. Zasoby są
wdrażane etapami: monitoring i operator PostgreSQL, baza oraz storage, a na
końcu Immich.

## Wymagania

- Kubernetes 1.29 lub nowszy.
- Domyślna klasa `StorageClass` obsługująca PVC `ReadWriteOnce`.
- Flux z obsługą `HelmRelease`, `HelmRepository` i `OCIRepository`.

Rozmiary wolumenów można zmienić w `controllers/monitoring/helmrelease.yaml`
oraz `storage/immich-library-pvc.yaml` i `storage/immich-database.yaml`.
Flux nie usuwa automatycznie klastra bazy ani PVC biblioteki; ich likwidację
wykonaj świadomie po wykonaniu kopii zapasowej.
Ingress nie jest konfigurowany, bo repozytorium nie określa kontrolera ani
domeny; aplikacje pozostają dostępne wewnątrz klastra.

## Dostęp

Grafana:

```sh
kubectl -n monitoring port-forward svc/kube-prometheus-stack-grafana 3000:80
kubectl -n monitoring get secret kube-prometheus-stack-grafana \
  -o jsonpath='{.data.admin-password}' | base64 -d; echo
```

Immich:

```sh
kubectl -n immich get svc
```

Wybierz usługę serwera Immich i wykonaj do niej `kubectl port-forward`; port
serwera to `2283`.