---
title: "Introducing Darnit: checking project practices with evidence"
description: "How Darnit checks project practices against explicit controls, where human review matters, and what we are exploring for reproducible experiments."
date: 2026-11-13
author: "Dev"
author_github: "Daniel-park10"
reading_time: 5
type: "blog"
layout: "single"
---

An early false pass in Darnit's bit-for-bit reproducibility check (RE-03.01) was documented in [Darnit issue #445](https://github.com/darnitdevorg/darnit/issues/445). During an audit of `psf/requests`, the check found `SOURCE_DATE_EPOCH` in `publish.yml` and returned PASS even though it had not built the project or compared outputs. The issue was fixed in [PR #454](https://github.com/darnitdevorg/darnit/pull/454), which changed signal-only results to WARN and stated that reproducibility had not been verified.

That test captures a question behind Darnit: what evidence should be enough to satisfy a check?

Darnit is a pluggable framework for checking project practices against explicit software engineering controls. Its first supported standards implementation covers the [Open Source Project Security (OSPS) Baseline](https://baseline.openssf.org/). Darnit can inspect repository files, query platform settings, run local checks, and collect evidence for a maintainer to review.

At the time of writing in November 2026, Darnit's Baseline implementation tracks OSPS v2026.02.19. An audit result describes the evidence checked by its configured rules. It is not a certification, and it cannot replace maintainer judgment.

## Why repository files are only part of the picture

Templates are useful when starting a repository. A template can add a `SECURITY.md` file or a contribution guide. It cannot show whether a live GitHub setting still matches the project's security requirements, or whether the documented process is followed.

For example, a repository file can describe who may approve changes. The actual branch protection setting lives in the hosting platform. Darnit checks the evidence available for each control instead of treating a file's presence as proof that the whole requirement is met.

## How a control is checked

Darnit runs the checks configured for each control. Some checks can produce a conclusive result. Others collect evidence and leave part of the review to a maintainer.

| Check | Example evidence | What it can establish |
|---|---|---|
| Repository file | A `SECURITY.md` file exists | The file is present, not that the process is followed |
| Platform setting | A GitHub permission value | The value returned by the platform at check time |
| Model-assisted review | Text quoted from a repository file | Evidence for review under the control's configured rules |
| Manual review | Confirmation about outside collaborators | Context that an automated check cannot establish |

OSPS-AC-02.01 is one example. Darnit can query a GitHub organization's default repository permission and record the value it receives. That setting does not cover every outside collaborator, so the API result alone cannot finish the check. Darnit leaves that remaining question for a person to review.

This boundary matters when a check uses a language model too. A model can help interpret repository evidence, but the result follows the review rules configured for that control. Where a person must confirm the result, the model's assessment remains pending until that review happens.

## Why positive signals need a limit

Finding `SOURCE_DATE_EPOCH`, `reprotest`, or `diffoscope` in a workflow gives the check a signal to record; it does not show that independent builds produced identical outputs. A passing RE-03.01 result needs evidence that the outputs from independent builds match.

## Amber and reproducible research

Amber explores ways to record a research run and compare selected outputs against a claim and measurement rule supplied by the researcher. The aim is to make the run and chosen measurement easier to inspect and repeat.

An Amber result is intended to report whether a selected output matches that measurement rule. That result alone cannot establish that an experiment was scientifically sound or that a paper's broader conclusions are correct.

At publication, Amber's design and command interface were being evaluated. For current availability and usage information, check the [Darnit repository](https://github.com/darnitdevorg/darnit). This post does not document Amber commands or make claims about signing, identity, or data privacy.

## Try a version-pinned audit

This example uses Darnit v0.1.1 so the command stays tied to a specific version:

```bash
uvx --from "git+https://github.com/darnitdevorg/darnit@v0.1.1" darnit audit /path/to/repo
```

For current installation options and newer releases, see the [Darnit README](https://github.com/darnitdevorg/darnit#readme).

## Join the conversation

If you maintain an open source project, tell us where repository checks help and where they miss important context. If you work on reproducibility, share which measurements and outputs you need to evaluate a run.

Open an issue in the [Darnit repository](https://github.com/darnitdevorg/darnit) or join #darnit on the [OpenSSF Slack](https://openssf.slack.com/archives/C0BD6BQEEJJ).

