# 🧠 GitHub Copilot Orchestrator — Centrale Planning & Documentatiehub

## 🎯 Doel

De **Copilot Orchestrator** beheert alle projectplanning, requirements en documentatie in Markdown, binnen GitHub.
Deze orchestrator fungeert als de **centrale Orchestrator** die:

* Structuur biedt voor planningen, user stories en taken.
* Communicatie mogelijk maakt met uitvoerende orchestrators via GitHub of Azure DevOps.
* De workflow beschrijft in Markdown (niet in code).
* Automatisch detecteert duplicaten en inconsistenties in requirements.
* Bidirectionele synchronisatie ondersteunt tussen GitHub en Azure DevOps.

---

## 🧩 Architectuur Overzicht

### Hoofdonderdelen

| Component                  | Doel                                                                | Opmerkingen                                     |
| -------------------------- | ------------------------------------------------------------------- | ----------------------------------------------- |
| **Planning Orchestrator**  | Centrale hub voor requirements, sprintplanning en afhankelijkheden. | Markdown-first; beheerd door Copilot Agents.    |
| **Execution Orchestrator** | Uitvoering van taken o.b.v. de Markdown-planning.                   | Haalt data via GitHub / Azure DevOps pipelines. |
| **Copilot Agents**         | Automatiseren reviews, updates, en voortgang bijhouden.             | Communiceren via PR's en Issues.                |
| **SprintAgent**            | Subagent binnen de Planning Orchestrator voor sprintbeheer.         | Genereert sprintroadmaps in Markdown.           |
| **PlanAgent**              | Zet ideeën/notities om naar gestructureerde plannen.                | LLM + Markdown output.                          |
| **DocAgent**               | Houdt documentatie, changelogs en roadmaps up-to-date.              | Automatische release notes.                     |
| **GitAgent**               | Beheert commits, branches, tags en submodule-sync.                  | LibGit2Sharp / pygit2.                          |
| **DevOps Agent**           | Aanmaken User Stories, Features, Epics, Tasks met acceptatiecriteria.| Azure DevOps Boards API.                        |

---

## 🧩 Submodule

Alle orchestrators worden beheerd als **Git submodule** binnen de hoofdrepository van de projecten waarin ze gebruikt worden.
Deze Repo is de Orchestrator Repo (die als submodule gebruikt gaat worden).


## ⚙️ Workflow

### 1. Markdown als Waarheid

Alle requirements, sprintdoelen en afhankelijkheden worden in Markdown bijgehouden.
Copilot Agents gebruiken deze bestanden als inputbron.

### 2. Bidirectionele Synchronisatie

* Bij elke wijziging in Markdown-documenten worden GitHub Actions getriggerd.
* De Execution Orchestrator ontvangt updates via Pull Requests of commits.
* **Statusupdates** van Execution Orchestrator worden teruggesynchroniseerd naar Planning.
* Azure DevOps Wiki API voor documentatie sync.

### 3. Sprint Planning & Evaluatie

De **SprintAgent**:

* Genereert nieuwe sprintplannen (`/sprints/sprint-YYYY-MM.md`).
* Controleert afhankelijkheden in `/dependencies.md`.
* Verifieert dat alle taken toegewezen zijn aan agents of ontwikkelaars.
* **Evalueert sprints** na sluiting en genereert retrospectives.

### 4. Requirements & Acceptatiecriteria

* Automatische koppeling tussen requirements en features/issues.
* Acceptatiecriteria voor User Stories en Tasks.
* Detectie van duplicaten en inconsistenties.

### 5. Versiebeheer & Documentatie

* Automatische commit bij wijzigingen in lokale documentatie.
* Branches en tags beheer voor releases.
* Rollback mechanismen bij conflicten.
* Automatisch genereren van changelogs op basis van commits.
* Export naar PDF of Wiki formaat.

---

## 🔗 Communicatie tussen Orchestrators

| Richting             | Kanaal                | Inhoud                                    |
| -------------------- | --------------------- | ----------------------------------------- |
| Planning → Execution | GitHub / Azure DevOps | Nieuwe taken, updates, dependencies       |
| Execution → Planning | GitHub / Azure DevOps | Statusrapporten, resultaten, logbestanden |
| Cross-project        | Git Submodules        | Gedeelde dependencies tussen projecten    |

* **Feedback loops** voor continue status updates.
* **Projectstatus generatie** op basis van commitgeschiedenis.

---

## 🧠 Taken en Dependencies

### Taakstructuur

* Elke taak bevat metadata in YAML-frontmatter binnen Markdown:

```markdown
---
id: TSK-001
type: user-story | feature | epic | task
status: open | in-progress | done | blocked
depends_on: [TSK-000]
assigned_to: Copilot-Agent
priority: high | medium | low
acceptance_criteria:
  - Criterium 1
  - Criterium 2
sprint: 2025-11
---

### Beschrijving
Implementeer visuele synchronisatie tussen planning en uitvoeringsdata.
```

### Dependency Management

* **DAG (Directed Acyclic Graph)** analyse voor taken.
* Identificatie van kritieke paden.
* Detectie welke taken sequentieel vs parallel kunnen.
* Cross-project dependencies via submodules.

---

## 🧩 Automatisering

| Agent                         | Doel                                                | Output                      |
| ----------------------------- | --------------------------------------------------- | --------------------------- |
| **Copilot Orchestrator Core** | Leest alle Markdown en genereert statusoverzichten. | `summary.md`                |
| **SprintAgent**               | Plant sprints en koppelt taken.                     | `sprints/sprint-YYYY-MM.md` |
| **PlanAgent**                 | Converteert ruwe input naar projectplannen.         | `plans/*.md`                |
| **DocAgent**                  | Genereert documentatie en release notes.            | `changelogs/*.md`           |
| **GitAgent**                  | Beheert versiebeheer en submodule sync.             | Commits, tags, branches     |
| **DevOps Agent**              | Synchroniseert met Azure DevOps Boards.             | User Stories, Features      |
| **SyncAgent**                 | Houdt GitHub ↔ Azure DevOps gesynchroniseerd.       | `PR` naar hoofdrepo         |
| **DependencyAgent**           | Controleert afhankelijkheden en kritieke paden.     | `dependencies.md` updates   |

---

## 🔄 Azure DevOps Integraties

| Functionaliteit              | Doel                                          | API/Tool                    |
| ---------------------------- | --------------------------------------------- | --------------------------- |
| **Wiki Synchronisatie**      | Documentatie exporteren naar DevOps Wiki      | Azure DevOps Wiki API       |
| **Boards Koppeling**         | User Stories, Features, Epics beheren         | Azure DevOps Boards API     |
| **Hiërarchie Management**    | Epic → Feature → User Story → Task structuur  | Work Item Tracking API      |
| **Sprint Management**        | Sprint planningen synchroniseren              | Iterations API              |

---

## 📅 Roadmap

| Fase       | Beschrijving                                                                 | Verwachte Output                            |
| ---------- | ---------------------------------------------------------------------------- | ------------------------------------------- |
| **Fase 1** | Setup van de Planning Orchestrator als Markdown repo + submodule integratie. | Werkende basisstructuur.                    |
| **Fase 2** | Toevoegen van alle agents (Plan, Doc, Git, DevOps).                         | Complete agent suite.                       |
| **Fase 3** | Bidirectionele sync met Execution Orchestrator via GitHub/DevOps.            | Volledig gesloten workflow met feedback.    |
| **Fase 4** | DAG analyse en kritieke pad detectie implementeren.                          | Geoptimaliseerde taakplanning.              |
| **Fase 5** | Cross-project dependency management via submodules.                          | Multi-project orchestratie.                 |
| **Fase 6** | Review en optimalisatie van agents via Copilot feedbackloops.                | Continue verbetering.                       |

---

## ✅ Samenvatting

* Alles draait om **Markdown-documentatie** met Copilot Agents als intelligente assistenten.
* **Bidirectionele synchronisatie** tussen Planning en Execution Orchestrators.
* **Volledige Azure DevOps integratie** inclusief Wiki, Boards en hiërarchieën.
* **Advanced dependency management** met DAG analyse en kritieke paden.
* **Submodules** zorgen voor scheiding van verantwoordelijkheden en cross-project integratie.
* Synchronisatie via GitHub/DevOps garandeert consistentie tussen planning en uitvoering.
* **Automatische documentatie generatie** inclusief changelogs en release notes.

---

## 🚀 Quick Start

```bash
# Clone de orchestrator als submodule
git submodule add https://github.com/your-org/planning-orchestrator orchestrator
git submodule update --init --recursive

# Configuratie instellen
cp orchestrator/config.yaml.example orchestrator/config.yaml
# Edit config.yaml met je GitHub/Azure DevOps tokens

# Eerste sprint aanmaken
cd orchestrator
python agents/sprint_agent.py --new-sprint 2025-11
```

---

Wil je dat ik dit plan omzet naar een **README.md** structuur (met badges, secties en instructies voor setup in GitHub)? Dat maakt het direct bruikbaar als projectbasis.