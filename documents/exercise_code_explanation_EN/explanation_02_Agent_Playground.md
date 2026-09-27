# Explanation: 02 Agent Playground

This notebook is a general playground for experimenting with an agent. It shows how one small change in the role, tone, data, prompts or recipe affects local results and the model response.

## 1. Preparing the playground

### S01 - loading helpers

The notebook imports `pprint` and items from `workshop_support.py`, including `AgentSession`, `SimulationClient`, `package_check`, `connect`, `run_coach` and `show_result`.

`package_check()` checks the selected kernel and required packages. The `result`, `custom_result` and `simulated_result` variables are initially set to `None`.

### S02 - role cards

`ROLES` contains two fictional roles: `project_coordinator` and `data_analyst`. The `ROLE_ID`, `FOCUS` and `TONE` variables select the current role, skill and tone.

### S03 - experience records

`EVIDENCE` contains fictional experience records. Each record has an identifier, skills, situation, task, action and result. These are classroom data, not real CVs.

### S04 - reading a role

`get_role(role_id)` checks whether a role exists in `ROLES`. It returns `found` with the role card or `unsupported` with the list of allowed roles.

### S05 - finding evidence

`get_evidence(skill)` filters `EVIDENCE` by skill. It returns `found`, `missing` or `unsupported`. Missing evidence must remain missing evidence.

### S06 - questions and tones

`QUESTIONS` contains questions for skills, while `TONES` contains question openings such as `friendly` and `direct`.

### S07 - one practice question

`get_question(skill, tone)` checks the inputs and combines the selected opening with the question. It works locally, without the model.

### S08 - three skill recipes

`SKILLS` describes three model tasks: role decoding, working with STAR evidence and question practice. `ACTIVE_SKILLS` specifies which recipes are active.

### S09 - fact and safety rules

`RULES` contains rules such as not inventing experience, metrics, employers or qualifications. `INSTRUCTIONS` combines these rules with the active recipes.

### S10 - allowed tools

`TOOL_RULES` registers three tools: `get_role`, `get_evidence` and `get_question`. `describe_tools()` builds the schema passed to the model.

### S11 - executing only an allowed tool

`execute_tool()` passes the request to `execute_safe()`. Python checks the tool name, arguments and allowed values again. A name that is not in the registry, such as `send_email`, is rejected.

### S12 - limited agent loop

`run_coach()` runs this loop:

1. creates an `AgentSession`,
2. sends a task to the model,
3. reads tool requests,
4. checks them locally,
5. executes permitted tools,
6. returns the results to the model,
7. ends with a response or a limit.

One session has a maximum of 5 model requests and 6 tool calls.

### S13 - basic task

`make_request()` builds `REQUEST` from the role, skill and tone. This is the text the agent must carry out.

### S14 - refreshing settings

`refresh_agent()` rebuilds `INSTRUCTIONS`, `REQUEST` and `TOOLS` after settings change. This matters because changing an earlier cell does not automatically change values already held in the kernel's memory.

### S15 - clean experiment start

`reset_experiment()` restores the baseline settings. This lets each experiment start from a comparable point, so changes from a previous experiment do not carry over.

### S16 - preparing runs

The notebook sets, among other things:

```python
CONNECT = False
RUN_LIVE = False
MAX_LIVE_RUNS = 2
live_runs = 0
runs = []
```

`runs` stores snapshots of completed attempts in memory. The limit of two attempts is a workshop restriction, not a Google account limit.

## 2. Establishing the baseline

First run the baseline settings locally. Check the role, evidence, question, instructions, tools and task before using the model.

## 3. Private connection

`CONNECT = False` means there is no connection. After changing it to `True`, `connect()` asks for a key in a hidden prompt and creates a client.

The connection itself does not send a request. Never enter the key into code, the notebook or a message.

## 4. One experiment run

The shared run cell checks, in order:

- whether `RUN_LIVE` is enabled,
- whether a client exists,
- whether two attempts have already been used,
- whether the user entered `RUN`.

It then refreshes the settings, increments the counter, calls `run_coach()`, stores a snapshot and displays the result.

`draft_needs_review` means a draft requiring human review, not a technical error.

## 5. Experiments E01-E08

The experiments do not send an API request by themselves. First they change one thing and run a local test. Only the shared run cell can make a live attempt.

### E01 - more direct tone

Changes `TONE` from `friendly` to `direct`. The local check confirms that the question starts with `Be specific.`. The facts in the evidence should not change.

### E02 - another role

Changes `ROLE_ID` to `data_analyst`. The card contains Python and SQL, but the presence of a requirement does not itself prove the candidate's experience.

### E03 - focus on planning

Changes `FOCUS` to `planning`. The local result should contain only record `event-1`. The practice question also changes.

### E04 - honest lack of experience

Changes `FOCUS` to `negotiation`. The function returns:

```python
{"status": "missing", "records": []}
```

A correct response should admit the lack of evidence and suggest practice instead of inventing a success story.

### E05 - shorter response

Adds to the prompt:

```python
PROMPT_ADDON = "Keep the complete answer under 100 words."
```

Check whether the shortened response still contains the facts, practice question and next step. The model may not follow the exact word limit.

### E06 - a more structured STAR recipe

Changes only `star_coach`, requiring four headings: Situation, Task, Action and Result. Evidence and permissions remain unchanged. Headings alone do not guarantee that the response is truthful.

### E07 - changing a tool question

Changes the data in `QUESTIONS`, then checks whether `get_question()` returns the new question. This is an experiment with tool data, not with model instructions.

### E08 - encouraging tone

Adds the `encouraging` tone, then refreshes the allowed arguments and tool schema. Changing `TONES` without refreshing the registry would cause the argument to be rejected as disallowed.

## 6. Prompt-addition menu

`PROMPT_MENU` contains ready-made additions to the task, such as a request for simpler language, a shorter answer or a next step. `PROMPT_CHOICE` selects one addition.

The addition changes the instruction, but not local permissions or facts.

## 7. Comparing observations

Saved snapshots can be reviewed without another request. Check:

- whether each personal claim has evidence,
- whether the response does not claim someone else's work,
- whether missing details are labelled,
- whether suggestions are labelled as future ideas,
- whether the trace shows the tools used.

One pair of results does not prove that a change always improves agent behavior. Impressive wording does not replace fact checking.

## 8. No Gemini

You can continue locally: read the roles and evidence, choose an experiment, predict the result and run tests. Label such a result `NOT TESTED LIVE`.

A local test checks the data and Python, but does not prove that Gemini followed the instruction.

## 9. Recording what was learned

`MY_OBSERVATIONS` is used to record:

- what was changed,
- what the local test was,
- what the result showed,
- what limitation remained.

## 10. Safe shutdown

At the end, the client is closed and the switches are disabled:

```python
client = None
CONNECT = RUN_LIVE = False
```

Before sharing the notebook, save the file, set live modes to `False`, remove sensitive data and clear outputs if they contain private information.
