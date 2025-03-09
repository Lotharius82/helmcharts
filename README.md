# Helm Charts Repository

Detta repository innehåller Helm charts för olika applikationer som används i min hemmainfrastruktur.

## Tillgängliga Charts

- [heimdall](charts/heimdall/): Heimdall Application Dashboard

## Installation

För att använda dessa charts, lägg till detta repo till din Helm-konfiguration:

```bash
helm repo add lotharius https://lotharius82.github.io/helmcharts
helm repo update
```

Sedan kan du installera chartet med:

```bash
helm install heimdall lotharius/heimdall -n homeservices
```

## Utveckling

För att lägga till ett nytt chart:

1. Skapa en ny katalog under `charts/`
2. Utveckla chartet
3. Paketera chartet: `helm package charts/ditt-chart -d docs`
4. Uppdatera index: `helm repo index docs --url https://lotharius82.github.io/helmcharts`
5. Committa och pusha ändringarna 