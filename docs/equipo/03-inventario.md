# Inventario de proyectos y estado del backup (medido, 2026-10-19)

Todo lo de abajo se midió en la máquina y en GitHub, no se estimó.
El objetivo de este documento es que nadie tenga que re-medir el parque de
proyectos para saber qué está respaldado y qué no.

## El número que ordena todo

**Toda la historia git de los 33 repos locales suma 421 MB.** Lo irreemplazable
—cada commit, cada rama, cada `reflog`— es medio giga. Los 43 GB del resto son
*working trees*, y de esos, 28 GB son artefactos regenerables de un solo
proyecto.

Consecuencia práctica: el respaldo del trabajo intelectual no necesita un disco
de 2 TB. Necesita `git remote` y, aparte, un canal para los pocos artefactos
que no se regeneran. Son dos problemas distintos y no conviene fusionarlos.

## Estado antes y después del respaldo

| Métrica | Antes | Después |
| --- | --- | --- |
| Repos en GitHub | 31 | **40** |
| Repos de trabajo sin `remote` | 11 | **0** |
| Commits sin subir | ~30 | **4** (decisión: no se suben) |
| Archivos sin commitear | ~194 | **79** (todos identificados) |

Los 79 restantes se explican en la sección *Qué queda fuera de cobertura*.

## Clasificación de los 33 repos locales

### Con remote propio y al día (sin acción)

`F1Show`, `GymApp`, `gemma-4-developer-agent`, `Hackeando_la_Mineria`,
`python-repo-evaluator`, `ml-deals-finder`.

### Publicados en este relevamiento

| Repo | Visibilidad | Contenido |
| --- | --- | --- |
| `CCD` | **Público** | Este repositorio. El PDF es un export de planilla, no una obra editorial |
| `Strategos` | Privado | Colectores MT5/Kraken, motor de replay, paper trading, specs OpenSpec |
| `kaggriculture` | **Público** | Agente de competencia Kaggle, 6 ramas |
| `Dots` | Privado | Scraper de ofertas mineras (`src/mining_jobs/`) |
| `kronos-lab` | Privado | 15 commits |
| `Distri` | Privado | 5 commits |
| `Coach-Tasks` | Privado | 2 commits |
| `gemma4-developer-agent` | Privado | Andamiaje de validación |
| `Nautilus_Own_MCP` | Privado | Servidor MCP de docs, specs OpenSpec |
| `Kprop` | Privado | Indicador Pine MTF de Kraken, reglas de riesgo |

`Kprop` ya existía en GitHub pero **vacío** (`isEmpty: true`): nunca se había
subido nada. Se llenó en este trabajo.

### Con remote, atraso resuelto

`R-Quant`, `chatai-platform`, `Bip_Max`, `fastapi-saas-starter` (5 ramas de PR),
`trajectory`, `arc-paper-track`, `Arbritraje`, `edge`, `CCBot`, `OpenShorts`,
`freelance-opportunity-radar`.

### Clones de terceros (no se respaldan, no son trabajo propio)

`flowsint` (reconurge), `open-design` (nexu-io), `kyutai-pocket-tts`
(kyutai-labs), `pi-nan-provider` (gtrabanco).

## Hallazgos de seguridad

Cuatro archivos o configuraciones habrían publicado credenciales o gigabytes si
se hubiera hecho un `git add .` sin revisar:

| Dónde | Qué era | Cómo se resolvió |
| --- | --- | --- |
| `kaggriculture/docs/kaggle-mcp-usage.md` | Token real de la API de Kaggle en texto plano | Redactado a `<YOUR_KAGGLE_API_TOKEN>`; **no estaba en la historia**, así que el repo público quedó limpio |
| `Strategos/tools/token` | Token en texto plano de 44 bytes | Agregado a `.gitignore`; verificado ausente del remoto |
| `Arbritraje` | `recordings.tar.xz` de **3,7 GB** | El `.gitignore` cubría el directorio `recordings/` pero no el archivo. Agregado `*.tar.xz` |
| `kaggriculture` | `scratch/` de **28 GB** y `docs/logs/` de 127 MB | Ambos agregados a `.gitignore` |

Otros ajustes: `Kprop` no tenía `.gitignore` (se creó), `CCBot` tenía el remote
en HTTPS sin credenciales (convertido a SSH) y tres repos no tenían identidad de
autor (se aplicó la ya establecida, por repo y sin tocar la configuración global).

**Pendiente del lado humano:** el token de Kaggle sigue siendo válido y estuvo
en texto plano. Corresponde rotarlo en *kaggle.com → Settings → API* y
actualizar `~/.pi/agent/mcp.json`. Redactar el archivo no invalida la credencial.

## Qué queda fuera de cobertura

| Bloque | Peso | Por qué no está respaldado |
| --- | --- | --- |
| `work/revelo` — fuente (docs, prompts, scripts) | 53 MB | Decisión: queda para el respaldo local, no se versiona en git |
| `work/revelo` — artefactos (traces, zips, evidence) | 3,35 GB | Un solo trace supera los 200 MB; GitHub rechaza archivos de más de 100 MB. Requiere `restic`, no git |
| `personal/.git` | — | Es un `git init` accidental: 0 commits, 0 ramas. Contiene `engram-project-identity.json`, el ancla de identidad de proyecto de Engram, así que **no se borró** |
| `kyutai-pocket-tts` | 3 archivos | Clon de terceros, no es trabajo propio |

**`revelo` no es un proyecto, es un área de trabajo.** Contiene cuatro trabajos
distintos —IaC Audit V3, OTS_TB_3.0 Athos, Terminal-bench 2.0 (más una copia
`-Old`) y Vulnerability Audit Submission— de los cuales solo 53 MB de 3,4 GB son
fuente. Un `git add .` ahí fallaría.

## El bloqueo real: no hay destino de backup

`restic`, `rsync` y `deja-dup` están instalados, pero **no hay ningún disco
externo montado**: el único filesystem de datos es `/` en `nvme0n1p2` (ext4, sin
LUKS). Mientras eso no cambie, decir "backup local" no significa nada: los
3,35 GB de `revelo` siguen en un solo disco sin cifrar.

El orden que desbloquea todo lo demás:

1. Abrir la tapa y verificar si el SN530 instalado es **2230** o **2280**
   (ver `01-sistema-operativo.md`; no se deduce por software).
2. Comprar el SSD NVMe interno de 2 TB y la carcasa USB-C para el SN530.
3. Montar `restic` con destino en el disco externo, excluyendo `scratch/`,
   `data/`, `.venv/`, `*.tar.xz` y `.cache`.
4. Reinstalar limpio sobre el disco nuevo con LUKS y volumen de laboratorio
   separado (una sola migración, no dos).

## Cómo volver a medir esto

```sh
# Repos locales y su estado
find ~/dev/projects -maxdepth 4 -name .git -type d -prune | sed 's|/\.git$||'

# Commits sin subir, contando TODAS las ramas (no solo la actual)
git log --branches --not --remotes --oneline | wc -l

# Repos en GitHub, detectando vacíos y archivados
gh repo list --limit 300 --json name,visibility,pushedAt,isEmpty,isArchived
```

`git log --branches --not --remotes` es la métrica correcta de trabajo sin
respaldar: un `git push` normal deja commits huérfanos en ramas sin upstream.
Para esos casos hace falta `git push origin --all`.
