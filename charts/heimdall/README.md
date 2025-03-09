# Heimdall Helm Chart

Detta Helm chart installerar [Heimdall](https://heimdall.site/) - en applikationsdashboard för hemanvändning.

## Installation

```bash
helm repo add lotharius https://raw.githubusercontent.com/Lotharius82/helmcharts/main/docs
helm repo update
helm install heimdall lotharius/heimdall -n homeservices
```

## Konfiguration

Följande tabell listar de konfigurerbara parametrarna för Heimdall-chartet och deras standardvärden.

| Parameter | Beskrivning | Standard |
|-----------|-------------|----------|
| `replicaCount` | Antal repliker | `1` |
| `namespace` | Kubernetes namespace | `homeservices` |
| `image.repository` | Container image repository | `linuxserver/heimdall` |
| `image.tag` | Container image tag | `latest` |
| `image.pullPolicy` | Container pull policy | `IfNotPresent` |
| `service.type` | Kubernetes service typ | `ClusterIP` |
| `service.port` | Service port | `80` |
| `service.targetPort` | Container port | `80` |
| `ingress.enabled` | Aktivera ingress | `true` |
| `ingress.annotations` | Ingress annotations | `kubernetes.io/ingress.class: traefik-external` |
| `ingress.hostname` | Hostname för ingress | `heimdalltest.ionix.dev` |
| `ingress.entryPoints` | Traefik entryPoints | `["websecure"]` |
| `ingress.tls.enabled` | Aktivera TLS | `true` |
| `ingress.tls.secretName` | TLS secret namn | `traefik/ionix-prod-tls` |
| `resources.requests.cpu` | CPU requests | `100m` |
| `resources.requests.memory` | Memory requests | `128Mi` |
| `resources.limits.cpu` | CPU limits | `500m` |
| `resources.limits.memory` | Memory limits | `512Mi` |
| `persistence.enabled` | Aktivera persistent volym | `true` |
| `persistence.storageClassName` | Storage class namn | `""` |
| `persistence.accessMode` | PV access mode | `ReadWriteOnce` |
| `persistence.size` | PV storlek | `1Gi` |
| `persistence.mountPath` | Mount path i container | `/config` |

Specificera varje parameter med `--set key=value[,key=value]` när du kör `helm install`.

Exempel:

```bash
helm install heimdall lotharius/heimdall -n homeservices \
  --set ingress.hostname=heimdall.example.com \
  --set persistence.size=2Gi
```

Alternativt kan du skapa en YAML-fil med värdena och använda den:

```bash
helm install heimdall lotharius/heimdall -n homeservices -f values.yaml
``` 