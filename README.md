# Women in Tech Insight: Workshop Materials

This repository contains the complete participant materials for the **Women in Tech Insight** workshop on building a small recruitment-practice coach with Python, Jupyter, VS Code and Gemini.

The workshop is delivered in English and is designed for individual work. The guided lab takes approximately **120 minutes** and is suitable for participants with mixed IT and non-IT backgrounds.

## Workshop repository

The official repository is:

**[github.com/ai-krk/woman_in_tech](https://github.com/ai-krk/woman_in_tech)**

The repository may be private before the workshop and made available by the organizers on the workshop day. Please use the access link provided by the organizers.

## Preparation repository

Complete the laptop setup using the separate preparation repository:

**[github.com/ai-krk/woman_in_tech_prep](https://github.com/ai-krk/woman_in_tech_prep)**

It contains the preparation PDF, the preflight notebook and the pinned requirements needed before the workshop. Please complete it at least 48 hours in advance.

## What you will build

You will create and inspect a small recruitment-practice coach for a fictional candidate preparing for an interview. The lab shows the difference between:

- what a model decides, such as whether to request a tool;
- what Python executes, such as a permitted role or evidence lookup; and
- what a person must still verify before trusting a generated draft.

The exercises include role and evidence lookups, interview-question practice, reusable coaching instructions, an agent loop, local permission checks, a labelled simulation and one small personal change.

## Files in this repository

- **`PREPARE_YOUR_LAPTOP_EN.pdf`** - Setup instructions to complete before the workshop. Allow 30–45 minutes and, where possible, finish at least 48 hours in advance.
- **`00_Preflight_EN.ipynb`** - Pre-workshop checks for Python, packages, the local environment and, when approved, Gemini access.
- **`01_Recruitment_Coach_EN.ipynb`** - The main 120-minute guided lab, *Build your Recruitment Coach*. It contains 26 short code cells covering model requests, Python tools, evidence checks, agent flow, local tests and a personal change.
- **`workshop_support.py`** - Helper functions used by the notebooks.
- **`requirements.txt`** - The pinned Python dependencies for the workshop environment.
- **`WORKSHOP_HANDBOOK_EN.html`** - A browser-friendly reference with copyable code, the worksheet, example solutions, theory and the complete source list. It does not execute Python or call a model.

## Recommended order

1. Open the [preparation repository](https://github.com/ai-krk/woman_in_tech_prep), read `PREPARE_YOUR_LAPTOP_EN.pdf` and complete the setup.
2. Open `00_Preflight_EN.ipynb` in desktop VS Code.
3. Select the workshop `.venv` kernel and run the preflight notebook one cell at a time.
4. Keep `workshop_support.py` in the same folder as the notebooks.
5. On the workshop day, open `01_Recruitment_Coach_EN.ipynb` when the facilitator asks.
6. Use `WORKSHOP_HANDBOOK_EN.html` as a browser reference while working through the lab.

The learning pattern is: **predict → run → inspect → edit one thing → check again**.

## If live access is unavailable

The notebook supports local exercises by default. If setup, network, quota or approved account access prevents a live run, continue with the clearly labelled simulation. A simulation exercises the local tools and workflow; it does not contain a new model decision and must not be presented as live Gemini output.

## Privacy and safe use

- Use fictional workshop records only. Do not enter real CVs, applications, candidate rankings, HSBC or client data, or private company information.
- Use live calls only after the organizer confirms the approved account and project arrangement.
- Enter your own organizer-approved AI Studio authentication key only into the notebook's hidden prompt.
- Never place a key in notebook source, chat, screenshots or shared files.
- Generated drafts are examples for learning and require human review. An evidence ID does not automatically prove every claim around it.
- Before sharing a notebook, set connection, live-run, simulation and save switches back to `False`, restart the kernel, clear outputs and check the saved source.

## Support

For setup or access questions, use the official contact channel provided by the workshop organizers before the workshop.