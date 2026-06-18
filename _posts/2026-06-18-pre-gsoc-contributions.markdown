---
layout: post
title: "Preparing for GSoC: My First Contributions to Autogits"
date: 2026-06-18 10:00:00 +0200
categories: gsoc opensuse autogits
---

Before the official GSoC coding period started, I began contributing to the
[`autogits`](https://src.opensuse.org/git-workflow/autogits) repository. These
initial tasks focused on making `obs-status-service` easier to develop, test,
and use.

`obs-status-service` exposes Open Build Service (OBS) build results as SVG,
JSON, or XML so they can be integrated into the openSUSE Git workflow. Before
working on the issues, I studied the service, the surrounding repository, and
the connection between Gitea, Redis, and OBS.

## Local Test Mode

My first contribution addressed
[`autogits` issue #113](https://src.opensuse.org/git-workflow/autogits/issues/113),
which requested a way to run `obs-status-service` locally without requiring a
real Redis server.

The implementation included:

* Creating a mock Redis client for local development.
* Providing a test mode that runs without an external Redis instance.
* Loading realistic OBS test data from a compressed JSON file.

I submitted these changes in
[pull request #118](https://src.opensuse.org/git-workflow/autogits/pulls/118).
This reduces the infrastructure required to start contributing. Developers can
run the service locally, inspect realistic project and package results, and
test changes without first configuring access to an OBS Redis server.

## Default Service Page

My second contribution worked on
[`autogits` issue #114](https://src.opensuse.org/git-workflow/autogits/issues/114).
Previously, visiting the root URL of `obs-status-service` only returned a 404
response. The goal was to provide a useful landing page for the service.

I created a small interface where users can enter an OBS project, package,
repository, and architecture. The page builds the corresponding status URL,
shows an SVG preview, and generates a Markdown snippet that can be copied into a
README. This makes the available endpoints easier to discover and avoids
requiring users to construct their URLs manually. I also separated the static
assets and used
[Pico CSS](https://picocss.com/) for lightweight styling.

This work was submitted in
[pull request #119](https://src.opensuse.org/git-workflow/autogits/pulls/119).

## Preparing the Project

Both contributions improve the entry points to the project: one makes the
development environment more accessible, while the other makes the service
more understandable for its users. They also established a useful foundation
for the main GSoC work on improving how OBS build results are generated,
displayed, and integrated into the Git workflow.
