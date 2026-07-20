---
layout: post
title: "GSoC Update 2: Visual Redesign and Responsive SVGs"
date: 2026-07-20 14:00:00 +0200
categories: gsoc opensuse svg golang
---

In my previous post I covered the template refactor for the `obs-status-service` SVG generation. That work was reviewed and [merged into main (PR #364)](https://src.opensuse.org/git-workflow/autogits/pulls/364). Building on top of that, I've opened a [follow-up PR (#402)](https://src.opensuse.org/git-workflow/autogits/pulls/402) that focuses on two things: making the SVG badges visually consistent with Gitea's UI and making them responsive for large projects.

## Native Gitea Colors

The old SVGs used hardcoded colors that looked out of place next to Gitea's UI. I extracted the real CSS variables from Gitea's dark and light themes and embedded them directly into the SVG templates. Now the badges blend in naturally with the rest of the page, and they automatically switch between light and dark mode using the `?theme=` parameter.

## Responsive Compact Mode

The project matrix view works great when there are a few repositories, but some projects like `openSUSE:Tools` have dozens of repo/arch combinations. The full matrix becomes too wide for any screen.

To solve this I added a `?compact=true|false|auto` parameter. In compact mode:

- Column headers are rotated 90° to take up less horizontal space.
- Badge text is hidden, leaving only colored squares.
- Column width shrinks from 155px to 32px.

The `auto` mode (the default) activates compact mode automatically when the matrix would exceed 800px wide or when there are more than 4 repositories. This is calculated server-side before generating the SVG, since CSS `@media` queries cannot resize an SVG's `viewBox` when it's embedded as an `<img>`.

## Dynamic Layout

Long package names like `000product:openSUSE-Addon-NonOss-ftp-ftp-x86_64` used to overflow and overlap with the status cells. Now both the label column width and the header height are calculated dynamically based on the longest text string, so nothing gets cut off regardless of the data.

## Side-by-side Comparisons

To verify the improvements, I wrote a Go test that fetches live build data from the OBS public API and generates SVGs with both the old production code (downloaded via HTTP from `br.opensuse.org`) and the new template-based renderer. Here are some examples using the projects my mentor dgarcia suggested:

### Project Summary: devel:languages:python:Factory

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 2rem;">
  <div style="flex: 1;">
    <p><strong>obs-status-service (old):</strong></p>
    <img src="{{ site.baseurl }}/assets/comparative/old/project_devel-languages-python-Factory.svg" alt="Old obs-status-service" style="max-width: 100%;">
  </div>
  <div style="flex: 1;">
    <p><strong>obs-status-service (new):</strong></p>
    <img src="{{ site.baseurl }}/assets/comparative/new/project_devel-languages-python-Factory.svg" alt="New obs-status-service" style="max-width: 100%;">
  </div>
</div>


### Package: devel:languages:python:Factory / python313

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 2rem;">
  <div style="flex: 1;">
    <p><strong>Before:</strong></p>
    <img src="{{ site.baseurl }}/assets/comparative/old/package_devel-languages-python-Factory_python313.svg" alt="Old package summary" style="max-width: 100%;">
  </div>
  <div style="flex: 1;">
    <p><strong>After:</strong></p>
    <img src="{{ site.baseurl }}/assets/comparative/new/package_devel-languages-python-Factory_python313.svg" alt="New package summary" style="max-width: 100%;">
  </div>
</div>

### Badge: openSUSE:Factory / python313:base / standard / x86_64

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 2rem;">
  <div style="flex: 1;">
    <p><strong>Before:</strong></p>
    <img src="{{ site.baseurl }}/assets/comparative/old/badge_python313_standard_x86_64.svg" alt="Old badge" style="max-width: 100%;">
  </div>
  <div style="flex: 1;">
    <p><strong>After:</strong></p>
    <img src="{{ site.baseurl }}/assets/comparative/new/badge_python313_standard_x86_64.svg" alt="New badge" style="max-width: 100%;">
  </div>
</div>

### Matrix View: devel:languages:python:Factory

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 2rem;">
  <div style="flex: 1;">
    <p><strong>gitexplorer (interactive):</strong></p>
    <img src="{{ site.baseurl }}/assets/comparative/gitexplorer_matrix_python-Factory.svg" alt="gitexplorer matrix" style="max-width: 100%;">
  </div>
  <div style="flex: 1;">
    <p><strong>obs-status-service (new):</strong></p>
    <img src="{{ site.baseurl }}/assets/comparative/new/matrix_devel-languages-python-Factory.svg" alt="New obs-status-service matrix" style="max-width: 100%;">
  </div>
</div>

## What's Next

The template refactor [PR #364](https://src.opensuse.org/git-workflow/autogits/pulls/364) has been merged into `main`. The Gitea color integration and compact mode are in review in [PR #402](https://src.opensuse.org/git-workflow/autogits/pulls/402).

The next step is exploring JavaScript inside SVGs so that badges can update themselves in real time without reloading the page.
