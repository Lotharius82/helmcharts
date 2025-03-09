# Heimdall Helm Chart

Detta Helm chart installerar [Heimdall](https://heimdall.site/) - en applikationsdashboard för hemanvändning.

## Installation

```bash
helm repo add lotharius https://lotharius82.github.io/helmcharts
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
| `ingress.tls.certResolver` | Traefik cert resolver (för wildcard-certifikat) | `default` |
| `ingress.tls.secretName` | TLS secret namn (används ej om certResolver är satt) | `traefik/ionix-prod-tls` |
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

## TLS-konfiguration och Wildcard-certifikat

Detta chart är nu konfigurerat för att enkelt kunna använda wildcard-certifikat som hanteras av Traefik utan att behöva en separat `sync_tls.yaml` för varje deployment.

### Två sätt att konfigurera TLS

1. **Använd Traefik's CertificateResolver (rekommenderas för wildcard-certifikat):**
   - Detta är den rekommenderade metoden när du har wildcard-certifikat konfigurerade i Traefik
   - Du behöver bara ange namnet på din CertificateResolver i Traefik (t.ex. "default", "le" eller annat namn du konfigurerat)
   - Traefik hanterar automatiskt certifikaten för alla domäner som matchar ditt wildcard
   - **Du behöver inte** skapa eller hantera separata Kubernetes secrets för varje tjänst

2. **Använd en specifik Kubernetes TLS-secret:**
   - Detta är den traditionella metoden som kräver att du skapar en TLS-secret för varje tjänst
   - Använd denna metod endast om du inte använder Traefik's certifikathantering

### Hur det fungerar

När `ingress.tls.certResolver` är angiven kommer Helm-chartet att konfigurera IngressRoute för att använda Traefik's inbyggda certifikathantering. Detta gör att:

- Traefik hanterar TLS-certifikaten automatiskt
- Du slipper skapa och underhålla separata `sync_tls.yaml`-filer för varje deployment
- Alla dina tjänster kan använda samma wildcard-certifikat som redan är konfigurerat i Traefik

### Exempel på användning

**1. Med wildcard-certifikat från Traefik:**

```bash
helm install heimdall lotharius/heimdall -n homeservices \
  --set ingress.hostname=heimdall.example.com \
  --set ingress.tls.certResolver=default
```

**2. Med en specifik TLS-secret (traditionell metod):**

```bash
helm install heimdall lotharius/heimdall -n homeservices \
  --set ingress.hostname=heimdall.example.com \
  --set ingress.tls.certResolver="" \
  --set ingress.tls.secretName=mitt-tls-certifikat
```

### Felsökning

Om du har problem med TLS-konfigurationen:

1. Kontrollera att din Traefik-installation har en korrekt konfigurerad CertificateResolver
2. Verifiera att namnet på CertificateResolver i values.yaml matchar det som är konfigurerat i Traefik
3. Om du använder secretName-metoden, kontrollera att TLS-secreten existerar i rätt namespace

För mer information om Traefik CertificateResolver, se [Traefik's officiella dokumentation](https://doc.traefik.io/traefik/https/acme/). 