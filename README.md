# Women in Tech Insight: Workshop Materials

This repository contains the complete participant materials for the **Women in Tech Insight** workshop on building a small recruitment-practice coach with Python, Jupyter, VS Code and Gemini.

The workshop is delivered in English and is designed for individual work. The guided lab takes approximately **120 minutes** and is suitable for participants with mixed IT and non-IT backgrounds.

## Workshop repository

The official repository is:

**[github.com/ai-krk/woman_in_tech](https://github.com/ai-krk/woman_in_tech)**

The repository may be private before the workshop and made available by the organizers on the workshop day. Please use the access link provided by the organizers.

## What you will build

You will create and inspect a small recruitment-practice coach for a fictional interview candidate. The lab shows the difference between:

- what a model decides, such as whether to request a tool;
- what Python executes, such as a permitted role or evidence lookup; and
- what a person must still verify before trusting a generated draft.

The exercises include role and evidence lookups, interview-question practice, reusable coaching instructions, an agent loop, local permission checks, a labelled simulation and one small personal change.

## Files in this repository

- **`00_Preflight_EN.ipynb`** - Checks for Python, packages, the local environment and, when approved, Gemini access.
- **`01_Recruitment_Coach_EN.ipynb`** - The main 120-minute guided lab, *Build your Recruitment Coach*. It contains 26 short code cells covering model requests, Python tools, evidence checks, agent flow, local tests and a personal change.
- **`02_Agent_Playground_EN.ipynb`** - A playground for changing roles, prompts, tones, evidence and agent recipes. This is an additional notebook, not covered during the workshop, for those who want to explore further.
- **`03_Software_Engineer_Interview_EN.ipynb`** - A fictional interview lab focused on Python, Java and debugging. This is an additional notebook, not covered during the workshop, for those who want to explore further.
- **`workshop_support.py`** - Helper functions used by the notebooks.
- **`requirements.txt`** - The pinned Python dependencies for the workshop environment.
- **`documents/WORKSHOP_HANDBOOK_EN.html`** - A browser-friendly reference with copyable code, the worksheet, example solutions, theory and the complete source list. It does not execute Python or call a model.

## Documents and structure

Supporting workshop documents are grouped in the `documents/` folder:

```text
documents/
├── Recruitment_Coach_EN.pptx
├── WORKSHOP_HANDBOOK_EN.html
├── exercise_code_explanation_EN/
│   ├── explanation_01_Recruitment_Coach_C01-C26.md
│   ├── explanation_02_Agent_Playground.md
│   └── explanation_03_Software_Engineer_Interview.md
└── exercise_code_explanation_PL/
	├── wyjasnienie_01_Recruitment_Coach_C01-C26.md
	├── wyjasnienie_02_Agent_Playground.md
	└── wyjasnienie_03_Software_Engineer_Interview.md
```

`Recruitment_Coach_EN.pptx` is the presentation slides shown during the workshop.

The `exercise_code_explanation_EN/` folder contains English explanations of the notebook cells, including for the optional `02_Agent_Playground_EN.ipynb` and `03_Software_Engineer_Interview_EN.ipynb` notebooks. The matching Polish explanations are kept in `exercise_code_explanation_PL/`.

## Recommended order

1. Open `00_Preflight_EN.ipynb` in desktop VS Code.
2. Select the workshop `.venv` kernel and run the notebook one cell at a time.
3. Keep `workshop_support.py` in the same folder as the notebooks and use the explanations in `documents/exercise_code_explanation_EN/` as a reference when needed.
4. Open `01_Recruitment_Coach_EN.ipynb` when the facilitator asks.
5. Use `documents/WORKSHOP_HANDBOOK_EN.html` as a browser reference while working through the lab.
6. Optionally, once the guided lab is done, explore `02_Agent_Playground_EN.ipynb` and `03_Software_Engineer_Interview_EN.ipynb`. These are not covered during the workshop.

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