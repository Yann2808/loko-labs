# Loko Labs — Crash Test Framework
> Document de référence interne · Version 1.0
> Utiliser ce framework pour chaque article "Crash Test" publié sur Loko Labs.

---

## Le verdict standard

Chaque crash test se conclut par **un seul verdict** parmi trois :

| Verdict | Label | Signification |
|---|---|---|
| ✅ | **PRODUCTION READY** | Tu peux l'adopter en prod. Stable, maintenu, intégrable. |
| ⚡ | **WATCH LIST** | Prometteur mais pas encore mature. À surveiller. |
| ❌ | **HYPE ONLY** | Le marketing dépasse la réalité. Évite pour l'instant. |

> **Règle d'or** : le verdict doit pouvoir se justifier en 2 phrases. S'il faut 10 lignes de nuances, c'est WATCH LIST.

---

## Durée minimale d'un crash test

| Type d'outil | Durée minimale | Pourquoi |
|---|---|---|
| Framework de transformation | 2 semaines | Les edge cases apparaissent à l'usage réel |
| Orchestrateur | 2 semaines | Il faut tester les failures et le retry |
| Base de données / moteur | 1 semaine | Les perfs se mesurent vite, la stabilité moins |
| Streaming & pipelines | 2 semaines | Le comportement sous charge est clé |

---

## Environnement de test standard

Pas de prod. Un environnement **contrôlé et reproductible** :

```bash
# Principe : tout doit être reproductible depuis zéro
# Docker Compose pour l'isolation
# Dataset de référence versionné sur GitHub
# Métriques capturées automatiquement (pas à la main)
```

**Dataset de référence Loko Labs :**
- Petit : 100k lignes (~10 MB)
- Moyen : 5M lignes (~500 MB)
- Grand : 50M lignes (~5 GB)

Toujours tester sur les 3 tailles. Les surprises arrivent souvent entre Moyen et Grand.

---

## Catégorie 1 — Frameworks de transformation
> dbt-core, SQLMesh, Coalesce, Dataform...

### Indicateurs

| # | Indicateur | Ce qu'on mesure | Poids |
|---|---|---|---|
| 1 | **Installation & First Run** | Temps de zéro à premier modèle qui tourne | ⭐⭐⭐ |
| 2 | **Qualité du SQL généré** | Lisibilité, optimisation, gestion des incremental | ⭐⭐⭐ |
| 3 | **Tests unitaires** | Facilité d'écriture, couverture, vitesse d'exécution | ⭐⭐⭐ |
| 4 | **Gestion des environnements** | Dev / Staging / Prod isolés nativement ? | ⭐⭐⭐ |
| 5 | **CI/CD natif** | GitHub Actions en moins de 30 lignes ? | ⭐⭐ |
| 6 | **Lineage & documentation** | Visualisation des dépendances, doc auto-générée | ⭐⭐ |
| 7 | **Gestion des erreurs** | Messages clairs ? Debug rapide ? | ⭐⭐ |
| 8 | **Communauté & maturité** | Slack actif, issues résolues, releases régulières | ⭐⭐ |
| 9 | **Modèle économique** | Open source pur ou fonctionnalités cachées derrière un paywall ? | ⭐ |

### Questions clés à se poser
- Est-ce que je peux migrer depuis dbt sans tout réécrire ?
- Les tests échouent-ils de façon utile (message d'erreur actionnable) ?
- Est-ce que la CI tourne en moins de 5 minutes sur le dataset moyen ?
- Y a-t-il un mode "slim" pour ne pas charger tout le warehouse en CI ?

### Scénario de test
```
1. Installation from scratch → chronomètre
2. Créer 3 modèles : staging / intermediate / mart
3. Écrire 5 tests : not_null, unique, accepted_values, custom
4. Simuler un échec intentionnel → observer le message d'erreur
5. Brancher sur GitHub Actions → mesurer le temps de CI
6. Tester le mode incremental sur dataset Grand
7. Générer la doc → est-elle exploitable ?
```

---

## Catégorie 2 — Orchestrateurs
> Dagster, Airflow, Prefect, Mage, Kestra...

### Indicateurs

| # | Indicateur | Ce qu'on mesure | Poids |
|---|---|---|---|
| 1 | **Modèle de définition** | Code-first vs config ? Lisibilité des DAGs/assets | ⭐⭐⭐ |
| 2 | **Gestion des failures** | Retry automatique, alertes, dead letter queue | ⭐⭐⭐ |
| 3 | **Observabilité native** | Logs structurés, métriques, traçabilité des runs | ⭐⭐⭐ |
| 4 | **Déploiement** | Docker Compose suffisant ? Kubernetes obligatoire ? | ⭐⭐⭐ |
| 5 | **Backfill & replay** | Rejouer une période passée facilement ? | ⭐⭐⭐ |
| 6 | **Gestion des secrets** | Intégration native Vault / AWS Secrets Manager ? | ⭐⭐ |
| 7 | **UI / DX** | L'interface aide-t-elle vraiment au debug ? | ⭐⭐ |
| 8 | **Scalabilité** | Comportement avec 100 DAGs / 1000 runs simultanés | ⭐⭐ |
| 9 | **Modèle économique** | Self-hosted viable ? Cloud-only pour les features clés ? | ⭐⭐ |

### Questions clés à se poser
- Que se passe-t-il quand un job échoue à 3h du matin ?
- Peut-on rejouer seulement les tâches en échec sans tout relancer ?
- L'UI permet-elle de comprendre une failure en moins de 2 minutes ?
- Le déploiement initial prend-il moins d'une journée ?

### Scénario de test
```
1. Déploiement from scratch via Docker Compose → chronomètre
2. Créer un pipeline 5 étapes avec dépendances
3. Simuler 3 types de failure : timeout / exception / ressource manquante
4. Observer : alerte reçue ? En combien de temps ?
5. Effectuer un backfill sur 7 jours
6. Monter à 50 pipelines actifs → mesurer la dégradation UI
7. Brancher un secret depuis une variable d'env
```

---

## Catégorie 3 — Bases de données & moteurs
> DuckDB, ClickHouse, Polars, Apache Arrow, Druid...

### Indicateurs

| # | Indicateur | Ce qu'on mesure | Poids |
|---|---|---|---|
| 1 | **Performance brute** | Temps de requête sur les 3 tailles de dataset | ⭐⭐⭐ |
| 2 | **Empreinte ressources** | RAM et CPU consommés à l'exécution | ⭐⭐⭐ |
| 3 | **Formats supportés** | CSV, Parquet, JSON, Iceberg, Delta nativement ? | ⭐⭐⭐ |
| 4 | **Intégration Python** | API propre ? Compatible pandas/polars/arrow ? | ⭐⭐⭐ |
| 5 | **Comportement en limite mémoire** | Crash ou spill to disk proprement ? | ⭐⭐⭐ |
| 6 | **Requêtes complexes** | Window functions, CTEs récursifs, UDFs | ⭐⭐ |
| 7 | **Concurrence** | Lectures/écritures simultanées sans corruption | ⭐⭐ |
| 8 | **Persistance & durabilité** | ACID ? WAL ? Comportement après crash process | ⭐⭐ |
| 9 | **Écosystème** | Connecteurs BI, support cloud, extensions | ⭐ |

### Questions clés à se poser
- Est-ce que ça remplace vraiment un cluster Spark pour mon cas d'usage ?
- Que se passe-t-il quand le dataset dépasse la RAM disponible ?
- Est-ce que je peux l'embarquer dans un script Python sans serveur ?
- Le comportement est-il identique sur Mac, Linux et en container ?

### Scénario de test
```
1. Installation → chronomètre jusqu'à première requête
2. Benchmark requêtes simples : COUNT, GROUP BY, JOIN sur les 3 datasets
3. Benchmark requêtes complexes : window functions, subqueries imbriquées
4. Test limite mémoire : dataset > RAM disponible → comportement ?
5. 10 connexions simultanées en lecture → dégradation ?
6. Crash process en pleine écriture → intégrité des données ?
7. Intégration Python : lire depuis Parquet, écrire vers CSV, via pandas
```

### Métriques à capturer automatiquement
```python
import time, tracemalloc, psutil

tracemalloc.start()
start = time.perf_counter()

# → requête ici

elapsed = time.perf_counter() - start
mem_mb  = tracemalloc.get_traced_memory()[1] / 1024 / 1024
cpu_pct = psutil.cpu_percent(interval=1)

print(f"Temps    : {elapsed:.3f}s")
print(f"Mémoire  : {mem_mb:.1f} MB")
print(f"CPU      : {cpu_pct}%")
```

---

## Catégorie 4 — Streaming & pipelines
> Kafka, Benthos/Redpanda Connect, Apache Flink, Pulsar, NATS...

### Indicateurs

| # | Indicateur | Ce qu'on mesure | Poids |
|---|---|---|---|
| 1 | **Latence end-to-end** | Temps entre production et consommation d'un message | ⭐⭐⭐ |
| 2 | **Throughput** | Messages/seconde soutenus sans perte | ⭐⭐⭐ |
| 3 | **Garanties de livraison** | At-most-once / at-least-once / exactly-once natif ? | ⭐⭐⭐ |
| 4 | **Comportement sous pression** | Backpressure géré ? Messages perdus sous pic ? | ⭐⭐⭐ |
| 5 | **Opérabilité** | Facilité de monitoring, métriques Prometheus natives | ⭐⭐⭐ |
| 6 | **Configuration** | YAML lisible vs XML/propriétaire ? | ⭐⭐ |
| 7 | **Connecteurs natifs** | S3, PostgreSQL, HTTP, Kafka, BigQuery... combien ? | ⭐⭐ |
| 8 | **Replay & rétention** | Rejouer depuis un offset ? Rétention configurable ? | ⭐⭐ |
| 9 | **Déploiement minimal** | Single binary ? Docker simple ? Zookeeper obligatoire ? | ⭐⭐ |

### Questions clés à se poser
- Que se passe-t-il si le consumer tombe pendant 10 minutes ?
- Les messages dupliqués sont-ils gérés nativement ou à ma charge ?
- Est-ce que je peux monitorer le lag de consommation sans plugin externe ?
- Le déploiement initial est-il faisable sans une semaine de formation ?

### Scénario de test
```
1. Déploiement from scratch → chronomètre
2. Pipeline simple : source HTTP → transform → sink PostgreSQL
3. Benchmark throughput : monter progressivement jusqu'à saturation
4. Mesurer la latence p50 / p95 / p99
5. Simuler panne consumer 10 min → messages perdus ? dupliqués ?
6. Simuler pic x10 du throughput normal → backpressure ou perte ?
7. Redémarrage à froid → reprise depuis le bon offset ?
8. Vérifier les métriques Prometheus → visibles dans Grafana ?
```

---

## Template de conclusion d'article

À copier-coller à la fin de chaque crash test :

```markdown
## Verdict final

| Critère | Note | Commentaire |
|---|---|---|
| Installation & DX | /5 | ... |
| Performance | /5 | ... |
| Fiabilité | /5 | ... |
| Intégration CI/CD | /5 | ... |
| Maturité | /5 | ... |
| **Score global** | **/25** | |

### ✅ PRODUCTION READY / ⚡ WATCH LIST / ❌ HYPE ONLY

> [Justification en 2 phrases maximum]

**Pour qui ?** [Profil idéal de l'utilisateur]
**Pas pour qui ?** [Cas où l'outil est inadapté]
**Alternative si WATCH LIST/HYPE ONLY ?** [Ce que tu recommandes à la place]
```

---

*Loko Labs Crash Test Framework v1.0 — lokolabs.hashnode.dev*