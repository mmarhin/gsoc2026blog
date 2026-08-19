---
layout: post
title: "GSoC 2026 Final Report: Enhancing openSUSE Git Workflow"
date: 2026-08-15 12:00:00 +0200
categories: gsoc opensuse autogits golang svg gitea
---

* **Student:** Mario Marín Hinojosa ([GitHub](https://github.com/mmarhin) • [openSUSE Gitea](https://src.opensuse.org/mmarhin) • [LinkedIn](https://www.linkedin.com/in/mmarhin))
* **Organization:** [openSUSE Project](https://summerofcode.withgoogle.com/archive/2026/organizations/opensuse-project) (Large, ~350h)
* **Mentor:** Daniel García Moreno ([@dgarcia](https://src.opensuse.org/dgarcia))
* **Repository:** [`git-workflow/autogits`](https://src.opensuse.org/git-workflow/autogits) ([Proposal #253](https://github.com/openSUSE/mentoring/issues/253))

---

## 1. Project Overview & Objectives

In openSUSE, package maintainers rely on the **Open Build Service (OBS)** to build software packages across multiple distributions and architectures. The `autogits` repository hosts core automation services for openSUSE's Gitea forge (`src.opensuse.org`), including **`obs-status-service`**, a Go service deployed at `br.opensuse.org` that renders build results as SVG badges and matrices for repository READMEs and pull requests.

### Proposal Goals & Evolution
The main goal of the proposal was to modernize `obs-status-service` to make OBS build results more accessible and visually integrated into Gitea.

During the coding period, together with my mentor, we prioritized:
1. **Refactoring the core engine:** Replacing legacy manual XML/SVG string concatenation with modular Go `text/template` architecture.
2. **Modern UI & Theming:** Incorporating native Gitea light/dark CSS variables and responsive dimensions.
3. **Scalability & Performance:** Optimizing rendering for large distributions with thousands of packages like `openSUSE:Factory` by introducing aggregated Repository Summaries (`?mode=repos`) and Project Progress Bars (`?mode=bars`).
4. **Real-time Interactivity:** Investigating browser SVG execution environments and creating a client-side polling prototype.
5. **Ecosystem & Upstream Work:** Contributing UI improvements directly to upstream Gitea.

---

## 2. Deliverables & Technical Achievements

### Pre-GSoC Tooling & Developer Experience
* **Offline Mock Test Mode ([PR #118](https://src.opensuse.org/git-workflow/autogits/pulls/118) - Merged):** Created a `RedisClient` interface mocking strategy loading compressed OBS data (`factory.results.json.bz2`), enabling offline local testing via the `--test-run` flag.
* **Interactive Link Builder ([PR #119](https://src.opensuse.org/git-workflow/autogits/pulls/119) - Merged):** Replaced the root 404 handler with an interactive Pico CSS web UI that generates direct badge URLs, live SVG previews, and ready-to-copy Markdown snippets.

### Core Architecture & Template Engine
* **Go `text/template` & `go:embed` ([PR #364](https://src.opensuse.org/git-workflow/autogits/pulls/364) - Merged):** Replaced manual string formatting with modular, embedded templates (`shared.tmpl`, `status-badge.tmpl`, `package-summary.tmpl`, `project-summary.tmpl`, `project-matrix.tmpl`) with explicit XML data escaping and matrix integration tests.

### Theming, Responsiveness & Scalability
* **Native Gitea Theming ([PR #402](https://src.opensuse.org/git-workflow/autogits/pulls/402) - Merged):** Extracted official Gitea light/dark theme CSS variables and resolved `:root` selector styling bugs inside SVG containers.
* **Responsive Compact Mode ([PR #402](https://src.opensuse.org/git-workflow/autogits/pulls/402) - Merged):** Added `?compact=true|false|auto` with 90° rotated headers and square indicator cells for wide matrices.
* **Repository Summary View ([PR #451](https://src.opensuse.org/git-workflow/autogits/pulls/451) - Merged):** Implemented `?mode=repos` to aggregate builds by repository and architecture, providing a fast and lightweight overview for large distributions like `openSUSE:Factory`.
* **Project Progress Bars & Unified API ([PR #462](https://src.opensuse.org/git-workflow/autogits/pulls/462) - Merged):** Added `?mode=bars` and unified visualization query endpoints under a clean `?mode=` parameter (`repos`, `bars`, `compact`, `matrix`). Fixed link targets with `target="_top"`.

<div style="display: flex; gap: 20px; align-items: flex-start; margin: 1.5rem 0; flex-wrap: wrap;">
  <div style="flex: 1; min-width: 280px;">
    <p style="margin-bottom: 6px;"><strong>Before (Legacy String Concatenation):</strong></p>
    <img src="{{ site.baseurl }}/assets/comparative/old/package_python313.svg" alt="Legacy SVG" style="max-width: 100%; border-radius: 6px; border: 1px solid #30363d;">
  </div>
  <div style="flex: 1; min-width: 280px;">
    <p style="margin-bottom: 6px;"><strong>After (Go Templates & Native Gitea Theme):</strong></p>
    <img src="{{ site.baseurl }}/assets/comparative/new/package_python313.svg" alt="New Template-based SVG" style="max-width: 100%; border-radius: 6px; border: 1px solid #30363d;">
  </div>
</div>

### Real-Time SVG Interactivity & Polling
* **Browser Security & Execution Research ([`test-js`](https://src.opensuse.org/mmarhin/test-js)):** Tested SVG script execution across browser contexts (`<img>` sandboxes scripts, while `<object>` tags execute JavaScript in trusted Gitea contexts).
* **Throttled Polling Prototype ([PR #487](https://src.opensuse.org/git-workflow/autogits/pulls/487) - In Review):** Embedded client-side JavaScript in SVG templates that uses throttled `setTimeout` polling against JSON endpoints (`Accept: application/json`) to update badge text and CSS classes in real time without page reloads.

### Upstream Gitea Contributions
* **Reputation Labels Frontend ([Commit 1e5a39c](https://github.com/mmarhin/gitea/commit/1e5a39c887) - Proposal / Initial Implementation):** Implemented an initial proposal and frontend implementation for user and organization reputation badges across profile headers and explore pages (`/explore/users`, `/explore/organizations`).

---

## 3. Pull Requests & Code Contributions

### Primary Repository (`git-workflow/autogits`)

| PR | Title | Status | Description |
| :--- | :--- | :---: | :--- |
| [**#118**](https://src.opensuse.org/git-workflow/autogits/pulls/118) | `Implement initial test-run option (#113)` | **Merged** | Mock Redis client and `--test-run` mode with realistic test data. |
| [**#119**](https://src.opensuse.org/git-workflow/autogits/pulls/119) | `Add default landing page with link builder (#114)` | **Merged** | Root `/` interactive badge builder UI and Markdown generator. |
| [**#364**](https://src.opensuse.org/git-workflow/autogits/pulls/364) | `refactor(obs-status-service): migrate SVG rendering to templates` | **Merged** | Migration to Go `text/template` architecture with `go:embed`. |
| [**#402**](https://src.opensuse.org/git-workflow/autogits/pulls/402) | `feat: responsive compact mode, layout fixes & Gitea theming` | **Merged** | Gitea light/dark colors, dynamic column sizing, and compact view. |
| [**#451**](https://src.opensuse.org/git-workflow/autogits/pulls/451) | `feat: Restore Project Matrix SVGs and fix bugs` | **Merged** | SVG CSS selector fixes, link targets, and repository summaries. |
| [**#455**](https://src.opensuse.org/git-workflow/autogits/pulls/455) | `svg_fixes` | **In Review** | SVG fixes and enhancements branch consolidating recent updates. |
| [**#462**](https://src.opensuse.org/git-workflow/autogits/pulls/462) | `feat: Use '?mode=' param for SVG visualizations` | **Merged** | Unified `?mode=` API (`repos`, `bars`, `compact`, `matrix`). |
| [**#487**](https://src.opensuse.org/git-workflow/autogits/pulls/487) | `feat: dynamic SVG status badge polling and DOM updates` | **In Review** | Real-time client-side polling with throttled `setTimeout`. |

### Upstream & Research Repositories

| Item | Repository | Status | Description |
| :--- | :--- | :---: | :--- |
| [**Commit 1e5a39c**](https://github.com/mmarhin/gitea/commit/1e5a39c887) | `mmarhin/gitea` | **Proposal** | Initial implementation of frontend reputation labels in profiles and explore views. |
| [**test-js**](https://src.opensuse.org/mmarhin/test-js) | `mmarhin/test-js` | **Completed** | SVG JavaScript execution testbed across browser contexts. |

---

## 4. openSUSE Conference 2026 Presentation

During the program, I was invited to attend the **openSUSE Conference 2026 (oSC26)** in Nuremberg. I delivered a 4-minute lightning talk showcasing the project architecture, SVG modernization, and real-time status updates, and had the chance to meet and connect in personal with the openSUSE team and community.

* **Talk Recording:** [Watch the Lightning Talk on YouTube (4:02 mark)](https://youtube.com/watch?v=APTecWnsLFc&t=242)

<div style="display: flex; gap: 15px; margin: 1.5rem 0; flex-wrap: wrap;">
  <div style="flex: 1; min-width: 280px;">
    <img src="{{ site.baseurl }}/assets/images/osc26/presentation.png" alt="oSC26 Lightning Talk Presentation" style="border-radius: 8px; width: 100%; border: 1px solid #30363d;">
    <p style="font-size: 0.85em; color: #8b949e; text-align: center; margin-top: 6px;">Lightning talk at openSUSE Conference 2026</p>
  </div>
  <div style="flex: 1; min-width: 280px;">
    <img src="{{ site.baseurl }}/assets/images/osc26/group-photo.jpg" alt="openSUSE Conference 2026 Group Photo" style="border-radius: 8px; width: 100%; border: 1px solid #30363d;">
    <p style="font-size: 0.85em; color: #8b949e; text-align: center; margin-top: 6px;">openSUSE Conference 2026 group photo in Nuremberg</p>
  </div>
</div>

---

## 5. Current State & Future Work

* **Current Status:** All primary milestones are completed and tested. The core SVG enhancements and fixes are currently under review in the [`svg_fixes` branch (PR #455)](https://src.opensuse.org/git-workflow/autogits/pulls/455), alongside the dynamic polling prototype (PR #487).
* **Next Steps:**
  * Deploy the refactored service to production on `br.opensuse.org`.
  * Extend real-time polling to project matrix and repository summary views.
  * Integrate structured build status summaries into automated Gitea PR comment bots.

---

## 6. Acknowledgments

I would like to express my deepest gratitude to my mentor **Daniel García Moreno ([@dgarcia](https://src.opensuse.org/dgarcia))** for his constant mentorship, code reviews, and architectural guidance throughout the summer.

A special thanks to **Adam ([@adamm](https://src.opensuse.org/adamm))**, maintainer of the `autogits` repository, for his valuable feedback and insights on scaling the service for large OBS distributions like `openSUSE:Factory`.

Finally, thank you to the **openSUSE Community** and **Google Summer of Code** for this incredible opportunity.
