# Loko Labs — Fichier de contexte
> À coller en début de conversation pour reprendre le projet là où on en est.
> Dernière mise à jour : Mars 2026

---

## Qui je suis

**Yann Salako** — Étudiant M2 MIAGE Data & BI, Rennes.
Recherche stage Data Engineering / Big Data — Mars 2026.

**Stack maîtrisée** : Python, SQL, dbt-core, Prefect, Docker, Snowflake, Databricks, Delta Lake, Talend, Power BI, PostgreSQL, PySpark.
**Niveau** : Junior (< 2 ans d'expérience, 2 stages).
**Certifications** : Databricks Data Modeling Strategies, AWS Cloud Practitioner Essentials.

---

## Le projet : Loko Labs

Blog technique sur Hashnode — **lokolabs.hashnode.dev**
Positionnement : pas de tutoriels génériques. Crash tests d'outils, DataOps industriel, retours d'expérience honnêtes.
Signature : **"Solide comme un Loko"**

### Les 4 piliers éditoriaux
1. **Crash Test d'outils** — Production Ready ou hype marketing ?
2. **DataOps** — CI/CD pour la data, Terraform, observabilité
3. **Code & Infra** — Python, Rust, Go, DX
4. **Structures durables** — Architecture pensée pour durer

---

## Stack technique du blog

| Composant | Outil | Détail |
|---|---|---|
| Blog | Hashnode | lokolabs.hashnode.dev |
| Landing page | HTML statique | index.html — HTML/CSS/JS en un seul fichier |
| Hébergement | Cloudflare Pages | loko-labs.pages.dev |
| Repo | GitHub | github.com/Lokolabs/loko-labs |
| Données articles | API GraphQL Hashnode | Fetch côté client, pas de redéploiement |
| Branching | Git flow simple | main = prod, branches pour chaque feature |

### Décisions techniques prises
- **HTML statique** plutôt que Next.js — volontaire, documenté dans l'article #001
- **Cloudflare Pages** plutôt que GitHub Pages — migré après l'article #001, CDN mondial + previews PR
- **Pas de wrangler.jsonc** — projet créé en mode Pages directement dans le dashboard Cloudflare
- **API Hashnode** appelée côté client — pas de webhook, pas de GitHub Action nécessaire

---

## Articles publiés

### Article #001 — Programmé lundi 10h30
**Titre** : Pourquoi j'ai choisi un fichier HTML plutôt que Next.js pour ma landing page
**Slug** : `html-vs-nextjs-landing-page-ingenieur-data`
**Angle** : Arbitrage d'ingénieur sur la complexité — pas un tutoriel
**Verdict** : PRODUCTION READY ✓
**Fichiers produits** :
- `loko-labs-article-001.md` — contenu Markdown pour Hashnode
- `cover-article-001.png` — cover 1600×840
- `og-image-article-001.png` — OG image 1200×630

---

## Articles en cours / pipeline

### Article #002 — En préparation
**Sujet** : DuckDB vs Pandas — Crash Test
**Angle** : À partir de quel volume Pandas devient un problème, et quand DuckDB le remplace vraiment ?
**Dataset** : NYC Yellow Taxi Trips (Kaggle) — 3 tailles : 8M / 50M / 100M lignes
**Repo dédié** : `loko-labs-crash-tests/001-duckdb-vs-pandas/`
**Statut** : Dataset à télécharger, benchmark.py à construire

### Pipeline d'articles (ordre suggéré)
1. ✅ Article #001 — HTML vs Next.js (publié lundi)
2. 🔄 Article #002 — DuckDB vs Pandas (en cours)
3. 📋 Article #003 — Prefect vs Dagster (comparatif naturel — Prefect déjà utilisé)
4. 📋 Article #004 — SQLMesh : le dbt-killer ?
5. 📋 Article #005 — Great Expectations vs dbt tests
6. 📋 Article #006 — Terraform pour Data Engineers
7. 📋 Article #007 — Architecture Medallion sur DuckDB
8. 📋 Article #008 — Streaming from scratch : Redpanda + Benthos

---

## Crash Test Framework

Fichier de référence : `docs/crash-test-framework.md` (dans le repo loko-labs)

### Verdict standard
- ✅ **PRODUCTION READY** — Stable, maintenu, intégrable en prod
- ⚡ **WATCH LIST** — Prometteur mais pas encore mature
- ❌ **HYPE ONLY** — Marketing > réalité

### Durées minimales
- Framework transformation : 2 semaines
- Orchestrateur : 2 semaines
- Base de données / moteur : 1 semaine
- Streaming : 2 semaines

### Script de mesure standard (bases de données)
```python
import time, tracemalloc, psutil

tracemalloc.start()
start = time.perf_counter()
# → opération ici
elapsed = time.perf_counter() - start
mem_mb  = tracemalloc.get_traced_memory()[1] / 1024 / 1024
cpu_pct = psutil.cpu_percent(interval=1)
print(f"Temps : {elapsed:.3f}s | Mémoire : {mem_mb:.1f} MB | CPU : {cpu_pct}%")
```

---

## Fichiers produits (tous disponibles)

| Fichier | Description |
|---|---|
| `index.html` | Landing page complète |
| `loko-labs-article-001.md` | Article #001 en Markdown |
| `loko-labs-article-001.docx` | Article #001 en Word |
| `cover-article-001.png` | Cover Hashnode 1600×840 |
| `og-image-article-001.png` | OG Image 1200×630 |
| `loko-labs-crash-test-framework.md` | Grilles d'évaluation par catégorie |

---

## Prochaine action immédiate

**Article #002 — DuckDB vs Pandas**
1. Télécharger NYC Yellow Taxi dataset sur Kaggle
2. Créer repo `loko-labs-crash-tests` sur GitHub
3. Construire `benchmark.py` — mesures CPU/RAM/temps sur les 3 tailles
4. Lancer le test sur 1 semaine

---

*Loko Labs — lokolabs.hashnode.dev | github.com/Lokolabs/loko-labs*