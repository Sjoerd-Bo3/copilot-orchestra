# 🧠 GitHub Copilot Orchestrator — Centrale Planning & Documentatiehub

## 🎯 Doel

De **Copilot Orchestrator** beheert alle projectplanning, requirements en documentatie in Markdown, binnen GitHub.
Deze orchestrator fungeert als de **centrale Orchestrator** die:

* Structuur biedt voor planningen, user stories en taken.
* Communicatie mogelijk maakt met uitvoerende orchestrators via GitHub of Azure DevOps.
* De workflow beschrijft in Markdown (niet in code).

---

## 🧩 Architectuur Overzicht

### Hoofdonderdelen

| Component                  | Doel                                                                | Opmerkingen                                     |
| -------------------------- | ------------------------------------------------------------------- | ----------------------------------------------- |
| **Planning Orchestrator**  | Centrale hub voor requirements, sprintplanning en afhankelijkheden. | Markdown-first; beheerd door Copilot Agents.    |
| **Execution Orchestrator** | Uitvoering van taken o.b.v. de Markdown-planning.                   | Haalt data via GitHub / Azure DevOps pipelines. |
| **Copilot Agents**         | Automatiseren reviews, updates, en voortgang bijhouden.             | Communiceren via PR’s en Issues.                |
| **SprintAgent**            | Subagent binnen de Planning Orchestrator voor sprintbeheer.         | Genereert sprintroadmaps in Markdown.           |

---

## 🧩 Submodule

Alle orchestrators worden beheerd als **Git submodule** binnen de hoofdrepository van de projecten waarin ze gebruikt worden.
Deze Repo is de Orchestrator Repo (die als submodule gebruikt gaat worden)

### Structuur

* De **Planning Orchestrator** bevat alleen documentatie, schema’s en Markdown-workflows.
* De **Execution Orchestrator** bevat scripts en pipelines die taken uitvoeren.
* Sync gebeurt via **GitHub Actions** of **Azure DevOps Pipelines**.

---

## ⚙️ Workflow

### 1. Markdown als Waarheid

Alle requirements, sprintdoelen en afhankelijkheden worden in Markdown bijgehouden.
Copilot Agents gebruiken deze bestanden als inputbron.

### 2. Synchronisatie via GitHub / Azure DevOps

* Bij elke wijziging in Markdown-documenten worden GitHub Actions getriggerd.
* De Execution Orchestrator ontvangt updates via Pull Requests of commits.

### 3. Sprint Planning

De **SprintAgent**:

* Genereert nieuwe sprintplannen (`/sprints/sprint-YYYY-MM.md`).
* Controleert afhankelijkheden in `/dependencies.md`.
* Verifieert dat alle taken toegewezen zijn aan agents of ontwikkelaars.

---

## 🔗 Communicatie tussen Orchestrators

| Richting             | Kanaal                | Inhoud                                    |
| -------------------- | --------------------- | ----------------------------------------- |
| Planning → Execution | GitHub / Azure DevOps | Nieuwe taken, updates, dependencies       |
| Execution → Planning | GitHub / Azure DevOps | Statusrapporten, resultaten, logbestanden |

Er is **geen directe API-communicatie** — enkel via versiebeheer.

---

## 🧠 Taken en Dependencies

* Elke taak bevat metadata in YAML-frontmatter binnen Markdown:

```markdown
---
id: TSK-001
status: open
depends_on: [TSK-000]
assigned_to: Copilot-Agent
priority: high
---

### Beschrijving
Implementeer visuele synchronisatie tussen planning en uitvoeringsdata.
```

* Dependencies worden automatisch geanalyseerd door Copilot Agents om volgorde en parallelle uitvoering te optimaliseren.

---

## 🧩 Automatisering

| Agent                         | Doel                                                | Output                      |
| ----------------------------- | --------------------------------------------------- | --------------------------- |
| **Copilot Orchestrator Core** | Leest alle Markdown en genereert statusoverzichten. | `summary.md`                |
| **SprintAgent**               | Plant sprints en koppelt taken.                     | `sprints/sprint-YYYY-MM.md` |
| **SyncAgent**                 | Houdt submodules up-to-date.                        | `PR` naar hoofdrepo         |
| **DependencyAgent**           | Controleert afhankelijkheden.                       | `dependencies.md` updates   |

---

## 📅 Roadmap

| Fase       | Beschrijving                                                                 | Verwachte Output                            |
| ---------- | ---------------------------------------------------------------------------- | ------------------------------------------- |
| **Fase 1** | Setup van de Planning Orchestrator als Markdown repo + submodule integratie. | Werkende basisstructuur.                    |
| **Fase 2** | Toevoegen van SprintAgent en DependencyAgent.                                | Automatische planning en dependency checks. |
| **Fase 3** | Volledige synchronisatie met Execution Orchestrator via GitHub Actions.      | Volledig gesloten workflow.                 |
| **Fase 4** | Review en optimalisatie van agents via Copilot feedbackloops.                | Continue verbetering.                       |

---

## ✅ Samenvatting

* Alles draait om **Markdown-documentatie** met Copilot Agents als intelligente assistenten.
* Geen directe programmeertaal nodig — alles via GitHub workflows.
* **Submodules** zorgen voor scheiding van verantwoordelijkheden.
* Synchronisatie via GitHub/DevOps garandeert consistentie tussen planning en uitvoering.

---

Wil je dat ik dit plan omzet naar een **README.md** structuur (met badges, secties en instructies voor setup in GitHub)? Dat maakt het direct bruikbaar als projectbasis.
