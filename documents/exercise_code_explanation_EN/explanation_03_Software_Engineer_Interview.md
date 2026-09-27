# Explanation: 03 Software Engineer Interview

This notebook is a preparation lab for a fictional Junior Software Engineer interview. It shows how an agent works with evidence about Python, Java and debugging without inventing missing facts.

## 1. Getting to know the candidate and role

The notebook uses a fictional candidate and fictional position. The information is for practice, not a real job application.

Three important concepts are:

- evidence - a fact recorded in the supplied data,
- suggestion - an idea for a future exercise that is not a project fact,
- boundary - a local rule defining which tools may be used.

## 2. One-time scenario setup

### S01 - helpers and model

The notebook imports functions from `workshop_support.py` and sets the model name in `MODEL`.

### S02 - role card

`ROLES` contains the `junior_software_engineer` card. The card describes a fictional role and the skills needed for the exercises.

### S03 - Python evidence

`PYTHON_RECORD` describes a Python project. The record may be used only for claims that are actually present in it.

### S04 - Java evidence

`JAVA_RECORD` describes a Java library catalog project. It contains information about title searching and tests.

### S05 - debugging history

`DEBUG_RECORD` describes a case-insensitive search problem, a change to the comparison and the addition of a regression test.

### S06 - reading facts

`get_role(role_id)` returns the role card, while `get_evidence(skill)` filters records by skill. In this notebook, `get_evidence("java")` should find the Java and debugging records.

Values such as SQL or HTTP may be known as areas to learn, but if they are not in the records, the result should be `missing`.

### S07 - beginner questions

`QUESTIONS` and `TONES` contain questions and question openings. `get_question(skill, tone)` returns one allowed question.

### S08 - three read-only tools

`TOOL_RULES` registers three tools:

- reading a role,
- finding evidence,
- retrieving a question.

There is no tool for submitting an application, executing code or sending messages.

Attempts to use `send_email`, `send_application` or `run_code` should be rejected.

### S09 - fair-coach rules

`RULES` requires the coach to:

- use only supplied facts,
- separate the candidate's work from the team's work,
- label suggestions as suggestions,
- not invent results, users, frameworks or qualifications.

### S10 - types of help

`SKILLS` contains recipes such as `plain_explainer`, `project_story`, `mock_interviewer`, `feedback_coach`, `technical_followup` and `study_planner`.

Recipes tell the model how to help, but do not give it additional permissions.

### S11 - the same limited agent loop

`run_coach()` works as in the other notebooks:

1. creates a session,
2. sends a task,
3. receives tool requests,
4. Python checks them locally,
5. executes only permitted reads,
6. passes the results to the model,
7. ends with a draft or a limit.

The limit for one session is a maximum of 5 model requests and 6 tool attempts.

### S12 - one task at a time

`set_case(title, focus, recipe, prompt)` sets the current case:

- title,
- skill,
- recipe,
- prompt.

It then builds `CURRENT_CASE`, `REQUEST` and `INSTRUCTIONS`.

### S13 - small session budget

The code prepares a maximum of three live attempts for the whole notebook. This is a workshop safeguard against accidental repeats, not a Google account limit.

### S14 - checking the new role

Before using Gemini, local assertions check the role, records, missing skills and blocking of nonexistent tools.

## 3. Choosing a task

### P01 - interview goal

P01 prepares a basic task for the agent, such as help preparing for a Junior Software Engineer role. The case-selection cell itself does not send a request.

## 4. Private connection

`CONNECT = False` means there is no connection. After setting it to `True`, `connect()` asks for a Gemini key in a hidden prompt.

A connection does not yet mean that a request has been made. The key should not be placed in code, the notebook or a message.

## 5. One run of the selected case

The shared live cell is the only cell that sends model requests.

It checks:

- whether a case has been selected,
- whether a client exists,
- whether three attempts have already been used,
- whether the user entered `RUN`.

It then calls `run_coach()`, stores the result in `runs` and displays it with `show_result()`.

Each run starts a fresh conversation. The agent does not automatically remember the previous response.

## 6. Additional cases P02-P08

### P02 - simpler explanation

Asks for simple explanations of `unit test`, `regression test` and `API`, with examples.

A new teaching example must be labelled as an example, not as the candidate's experience.

### P03 - honest project story

Asks for an approximately 60-second answer about the Java project.

The response must separate personal contribution from a colleague's or team's work and must not add numbers, users or commercial results.

### P04 - one debugging question

Asks for one friendly debugging question and two short hints.

The agent should not immediately give the full answer because the goal is independent practice.

### P05 - feedback on an answer

`INTERVIEW_QUESTION` contains a question, and `MY_ANSWER` contains a sample practice answer.

A later prompt asks for:

- one thing done well,
- two details to improve,
- one follow-up question,
- a stronger fact-based version of the answer.

This is not an automatic continuation of the conversation. The question and answer must be sent again in a new task.

### P06 - technical follow-up questions

Asks for three questions about title searching and tests in the Java project.

Suggestions for additional tests must be labelled as ideas to try, not tests that have already been written.

### P07 - a small preparation plan

Asks for three 20-minute sessions:

- explain the project,
- practise testing or debugging,
- study a basic API or SQL concept.

API and SQL should be presented as areas to learn, not previous experience.

### P08 - challenging an exaggerated claim

The prompt contains an intentionally exaggerated version of the story, such as independently building a production platform for 1,000 people and improving performance by 90%.

The agent should identify every unsupported claim and rewrite the story so that it stays within the actual records.

Do not retain invented numbers as estimates.

## 7. Checking the agent's actual behavior

Saved results in `runs` can be reviewed without another request.

During the review, check:

- whether every personal claim has a specific record,
- whether the response separates the candidate's contribution from the team's,
- whether missing information is admitted,
- whether new examples are labelled as suggestions,
- whether the response answers the selected question,
- whether the trace shows use of the correct tools.

An evidence ID alone is not enough. Compare the claim with the content of the record.

## 8. Practice without the live API

If Gemini is unavailable, you can read the data, choose cases, predict responses and run local tests.

Label this work `NOT TESTED LIVE`. A local test checks code and data, but does not prove that the model followed the instructions.

The Java story included in the notebook is an authored classroom example, not a previous Gemini response.

## 9. Your own small improvement

Choose one small element to change, such as the prompt, tone, recipe structure or way of asking a question.

A good change should have:

- an observable effect,
- a local test,
- one limitation that still remains.

## 10. Safe shutdown

At the end, the client is closed and live switches are set to `False`:

```python
client = None
CONNECT = RUN_LIVE = False
```

Before sharing the notebook, save the changes, do not share the key, disable live mode and check that the results contain no private data.
