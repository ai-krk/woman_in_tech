# Explanation of notebook C01-C26

This document describes all parts of `01_Recruitment_Coach_EN.ipynb` in order.

## C01 - opening the workspace

The notebook uses a kernel, which is a running Python session that remembers variables between cells.

The first cell:

```python
from pprint import pprint
```

imports a readable way to display dictionaries and lists.

```python
from workshop_support import (...)
```

imports functions and classes from the local `workshop_support.py` file, including:

- `AgentSession` - manages the agent session,
- `SimulationClient` - runs a simulation without network access,
- `connect` - creates a Gemini client,
- `package_check` - checks the environment,
- `show_result` - displays a result.

```python
result = custom_result = simulated_result = None
```

Creates three result variables and sets them to empty values.

```python
pprint(package_check())
```

Checks the Python version, kernel path and required packages.

The notebook should use this environment:

```text
C:\tmp\woman_in_tech\.venv\Scripts\python.exe
```

When the environment is ready, `LOCAL NOTEBOOK READY` appears. Otherwise, continue only with local exercises until the configuration is fixed.

## C02 - private connection

```python
MODEL = MODEL_DEFAULT
```

Sets the default model name.

```python
client = None
CONNECT = False
```

At first there is no client and the connection is disabled.

After changing it to:

```python
CONNECT = True
```

`connect()` asks for a Gemini key in a hidden field and creates a client.

If the connection succeeds, `CLIENT READY` appears. Creating the client does not send a request to the model yet.

Errors are passed to `safe_error()` so that raw messages that could contain request data are not displayed.

## C03 - first response

```python
RUN_LIVE = False
```

Protects against accidentally sending a request.

After setting `RUN_LIVE = True`, the notebook asks you to enter `RUN`. If you confirm, it executes:

```python
first_response = run_demo(client, MODEL)
show_result(first_response)
```

This is one short request to the model without using tools.

A successful run should show, among other things:

```text
MODE: LIVE GEMINI | STATUS: draft_needs_review
```

The model response may vary. Check that the request was actually sent and that the result requires human review.

## C04 - selecting a role

`ROLES` is a dictionary with two fictional job cards:

- `project_coordinator`,
- `data_analyst`.

Each card contains a title and a list of skills.

```python
ROLE_ID = "project_coordinator"
FOCUS = "communication"
TONE = "friendly"
```

These variables select the current role, skill and tone.

```python
pprint(ROLES[ROLE_ID])
```

Displays the selected card. If you enter a nonexistent key such as `astronaut`, a `KeyError` appears.

This part works locally, without the model.

## C05 - evidence database

`EVIDENCE` is a list of fictional experience records.

Each record contains:

- `id`,
- `skills`,
- `situation`,
- `task`,
- `action`,
- `result`.

The first record concerns organizing an event and supports communication and planning. The second describes a project using Python.

```python
pprint(EVIDENCE[0])
```

Displays the first record in the list. Indexes start at zero, so `EVIDENCE[0]` is the first item.

The data is fictional. Do not add real CVs, client data or private information.

## C06 - the `get_role` tool

```python
def get_role(role_id):
```

Defines a function that looks up a role card.

```python
if not isinstance(role_id, str) or role_id not in ROLES:
```

Checks whether the identifier is text and exists in the `ROLES` dictionary.

For an invalid role it returns:

```python
{"status": "unsupported", "allowed_roles": list(ROLES)}
```

For a valid role it returns its data:

```python
{"status": "found", "role_id": role_id, **ROLES[role_id]}
```

`**ROLES[role_id]` unpacks the card data into a new dictionary.

The tool only reads local data. It does not evaluate a person or send a request to the model.

## C07 - the `get_evidence` tool

The function searches for experience related to a skill:

```python
get_evidence("communication")
```

Possible statuses:

- `found` - records were found,
- `missing` - the skill is known, but there is no evidence,
- `unsupported` - the skill is not supported.

For example, `get_evidence("sql")` reports missing evidence because no record contains SQL.

## C08 - question database

Creates the `QUESTIONS` and `TONES` dictionaries.

`QUESTIONS` contains questions by skill, and `TONES` contains question openings:

```python
"friendly": "Take a moment to think. "
"direct": "Be specific. "
```

The cell only creates data and should not send a request to the model.

## C09 - the `get_question` tool

```python
def get_question(skill, tone):
```

The function checks whether the skill and tone exist, then combines the opening with the question.

For example:

```python
get_question("communication", "friendly")
```

returns a question beginning with `Take a moment to think.`.

The function works locally.

## C10 - skill recipes

`SKILLS` contains instructions for the model:

- `role_decoder` - checks the role,
- `star_coach` - works with evidence and creates a STAR draft,
- `interview_practice` - asks a practice question.

`ACTIVE_SKILLS` specifies which recipes are active.

Here you can add your own sentence to one recipe, for example `Explain any jargon in plain English.`.

## C11 - assembling instructions

`RULES` contains the coach's main rules, such as not inventing experience, using only the supplied tools and providing evidence IDs.

```python
INSTRUCTIONS = RULES + "\n" + "\n".join(
    SKILLS[name] for name in ACTIVE_SKILLS
)
```

Combines the rules with the active recipes. `INSTRUCTIONS` will later be passed to the model.

## C12 - registering tools

`TOOL_RULES` describes the tools available to the agent:

- `get_role`,
- `get_evidence`,
- `get_question`.

For each tool it specifies the function, description and permitted arguments.

```python
TOOLS = describe_tools(TOOL_RULES)
```

creates the tool schema for Gemini.

## C13 - permission control

```python
def execute_tool(name, arguments):
    return execute_safe(name, arguments, TOOL_RULES)
```

The function passes the request to validation.

The model may name a tool `send_email`, but because it is not in `TOOL_RULES`, Python rejects it.

The key rule is:

> The model may propose an action, but Python decides whether it may be executed.

## C14 - the agent loop

`run_coach` describes the complete flow:

1. create an `AgentSession`,
2. send a task to the model,
3. read the response,
4. find tool requests,
5. check permissions,
6. execute permitted tools,
7. pass the results back to the model,
8. repeat or finish with a response draft.

Each session has a maximum of 5 model requests and 6 tool calls.

Defining the function does not run it yet.

## C15 - preparing the task

`make_request` builds the task text from the role, skill and tone:

```python
REQUEST = make_request(ROLE_ID, FOCUS, TONE)
```

The result may start like this:

```text
Prepare me for role project_coordinator. Focus on communication. Tone: friendly.
```

This is the task the agent will later receive.

## C16 - running the real coach

By default:

```python
RUN_LIVE = False
```

After setting it to `True` and entering `RUN`, this is executed:

```python
result = run_coach(REQUEST, client)
show_result(result)
```

At this point the model may request the use of the three local tools. The call is limited by a budget and has no automatic retries.

## C17 - checking the result

This cell shows what actually happened during the session.

```python
result["status"]
```

shows the status, while entries in `result["trace"]` show the actual tool results.

Do not assume that the model used a tool just because it mentioned it in the response. Check the `trace`.

## C18 - six local tests

The `assert` tests check local functions and permissions:

- communication has evidence,
- SQL correctly returns missing data,
- an unknown role is rejected,
- an unknown tone is rejected,
- `send_email` is blocked,
- an extra argument is rejected.

The message:

```text
6 LOCAL CHECKS PASSED: no model evaluated.
```

means that local code was checked, not the model.

## C19 - intentionally failing test

Changing:

```python
expected_status = "missing"
```

to:

```python
expected_status = "found"
```

causes an `AssertionError`, because the actual status for SQL is `missing`.

This exercise shows that a test detects an incorrect expectation. Restore `missing` after the experiment.

## C20 - the security boundary

The code creates text that imitates a malicious instruction and tries to call `send_email`.

Python rejects the attempt because the tool is not on the allowed list.

This is a test of local permission control. It is not a complete test of model resistance to prompt injection.

## C21 - personalizing the coach

The settings change, for example:

```python
ROLE_ID = "data_analyst"
FOCUS = "sql"
TONE = "direct"
```

You can also change the `star_coach` recipe.

The code rebuilds `INSTRUCTIONS` and `REQUEST`.

For SQL, the result should show:

```python
{"status": "missing", "records": []}
```

Missing data is an honest result, not an error.

## C22 - an additional tone

After setting:

```python
TRY_ADVANCED = True
```

an `encouraging` tone is added.

The code updates the tone data, argument rules, tool schema and task.

This shows that a new value must be added both to the data and to validation.

## C23 - comparing the changed coach

You can run another live request or compare locally:

```python
get_evidence(FOCUS)
get_question(FOCUS, TONE)
```

A difference in wording is not by itself evidence of improvement. Compare the facts, tools used and status.

## C24 - simulation

If the API does not work, set:

```python
RUN_SIMULATION = True
```

`SimulationClient` imitates responses and tool calls, but it is not a model and does not use the network.

The result must be labelled as a simulation, for example:

```text
SIMULATION: authored fixture, no model call
```

A simulation is a fallback plan, not evidence that the API is ready.

## C25 - reflection and saving

In `MY_LEARNING`, enter:

```python
MY_LEARNING = "My change was ... My test checked ... A remaining limitation is ..."
```

After setting:

```python
SAVE_NOTES = True
```

the text is saved to `my_coach_notes.md`.

If the file already exists, it is not overwritten.

## C26 - closing

If a client exists, the connection is closed:

```python
if client is not None:
    client.close()
```

Then:

```python
client = None
```

removes the reference to the client, and:

```python
CONNECT = RUN_LIVE = RUN_SIMULATION = SAVE_NOTES = False
```

disables all switches.

Before sharing the notebook, check that:

- `CONNECT` is set to `False`,
- `RUN_LIVE` is set to `False`,
- `RUN_SIMULATION` is set to `False`,
- `SAVE_NOTES` is set to `False`,
- the API key is not in the code or outputs,
- only fictional data was used.

The final message is:

```text
Client closed. Save your notebook and review it before sharing.
```
