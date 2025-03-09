# PiGallery2 Helm Chart

Detta Helm-chart installerar [PiGallery2](https://github.com/bpatrik/pigallery2) i ett Kubernetes-kluster.

## Introduktion

PiGallery2 är ett självvärderat bild- och videogalleri med stöd för ansiktsigenkänning, geotaggning och mycket mer.

## Förutsättningar

- Kubernetes 1.16+
- Helm 3.0+
- PV-provisioner stöd i underliggande infrastruktur (om persistence är aktiverat)

## Installation

### Lägg till Helm-repo

```bash
helm repo add lotharius82 https://github.com/Lotharius82/helmcharts.git
helm repo update
```

### Installera chartet

```bash
helm install pigallery2 lotharius82/pigallery2 -n homeservices
```

För att installera chartet med ett anpassat release-namn:

```bash
helm install mitt-galleri lotharius82/pigallery2 -n homeservices
```

## Konfiguration

Följande tabell listar de konfigurerbara parametrarna för PiGallery2-chartet och deras standardvärden.

| Parameter | Beskrivning | Standard |
| --------- | ----------- | ------- |
| `replicaCount` | Antal repliker | `1` |
| `namespace` | Kubernetes namespace | `homeservices` |
| `app.image.repository` | PiGallery2 image repository | `bpatrik/pigallery2` |
| `app.image.tag` | PiGallery2 image tag | `latest` |
| `app.image.pullPolicy` | Image pull policy | `IfNotPresent` |
| `app.service.type` | Kubernetes service type | `ClusterIP` |
| `app.service.port` | Service port | `80` |
| `app.ingress.enabled` | Aktivera ingress | `true` |
| `app.ingress.hostname` | Hostname för ingress | `foto.ionix.dev` |
| `app.persistence.config.enabled` | Aktivera persistence för config | `true` |
| `app.persistence.config.size` | Storlek på config PVC | `5Gi` |
| `app.persistence.tmp.enabled` | Aktivera persistence för tmp | `true` |
| `app.persistence.tmp.size` | Storlek på tmp PVC | `6Gi` |
| `app.persistence.images.enabled` | Aktivera persistence för bilder | `true` |
| `app.persistence.images.type` | Typ av volym för bilder (nfs eller pvc) | `nfs` |
| `app.persistence.images.server` | NFS-server för bilder | `192.168.1.6` |
| `app.persistence.images.path` | NFS-sökväg för bilder | `/mnt/ctstorage/Appdata/pigallery2/data` |
| `database.enabled` | Aktivera intern databas | `true` |
| `database.image.repository` | Databas image repository | `mariadb` |
| `database.image.tag` | Databas image tag | `latest` |
| `database.persistence.enabled` | Aktivera persistence för databas | `true` |
| `database.persistence.size` | Storlek på databas PVC | `5Gi` |
| `secrets.existingSecret` | Namn på befintlig secret | `mysql-secrets` |
| `secrets.create` | Skapa ny secret | `false` |

### Exempel på values.yaml

```yaml
replicaCount: 1
namespace: homeservices

app:
  ingress:
    hostname: mitt-galleri.exempel.se
  
  persistence:
    images:
      server: 192.168.1.100
      path: /mnt/data/bilder

database:
  persistence:
    size: 10Gi

secrets:
  create: true
  mysql:
    database: pigallery2
    user: pigallery2
    password: mittLosenord
    rootPassword: mittRootLosenord
```

## Avinstallation

För att avinstallera/ta bort chartet:

```bash
helm uninstall pigallery2 -n homeservices
```

Detta tar bort alla Kubernetes-komponenter som är associerade med chartet och tar bort releasen.

**OBS:** PersistentVolumeClaims tas inte bort automatiskt. Om du vill ta bort dem:

```bash
kubectl delete pvc -l app.kubernetes.io/instance=pigallery2 -n homeservices
``` 