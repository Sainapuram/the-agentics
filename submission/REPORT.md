# CrewAI Build Report

**Framework:** CrewAI  
**Topic:** Machine Learning in Healthcare  
**Experiment:** Changing the Technical Writer's backstory to target 10-year-old readers

## 1. What I Changed and Why

For this assignment, I selected the CrewAI starter kit and modified the Technical Writer agent's backstory. In the original implementation, the Technical Writer was instructed to turn dense technical material into clear, jargon-free prose. This was suitable for a general audience but did not specify an age group.

I changed the backstory to describe the agent as a friendly science teacher who explains complex topics to 10-year-old students. The new instructions encouraged simple vocabulary, short sentences, relatable examples, and minimal technical jargon.

My goal was to investigate whether changing an agent's role description could influence the readability and presentation of its output without modifying the underlying task structure. I kept the same topic, *Machine Learning in Healthcare*, for both executions to make the results easier to compare.

I did not intentionally change the Research Analyst, Editor, task descriptions, or sequential workflow as part of this experiment. Before the experiment, I also added `max_completion_tokens=4096` to the LLM configuration to address repeated empty-response errors. This was a compatibility adjustment rather than the main experimental change.

## 2. How the CrewAI Architecture Works

CrewAI organizes a workflow around agents, tasks, and a crew. In this starter kit, three agents collaborate to produce a written explanation.

The **Research Analyst** is responsible for collecting five key facts and identifying two open questions about the selected topic. Its backstory emphasizes careful analysis and acknowledging uncertainty.

The **Technical Writer** receives the research information and is responsible for preparing an approximately 250-word explanation in plain language. This agent was the focus of my modification because its instructions directly influence the intended audience and writing style.

The **Editor** reviews the explanation, improves clarity, removes unnecessary material, and flags claims requiring supporting sources.

Each agent is assigned a corresponding `Task` containing a description and an expected output. The tasks are assembled into a `Crew` configured with `Process.sequential`. This means the workflow proceeds from research to writing to editing, with information passed between tasks.

All three agents use the same Groq-hosted language model through CrewAI's `LLM` interface. The application accepts a topic from the command line, starts the crew using `crew.kickoff()`, and saves the final output in `output/explainer.md`.

This architecture is appropriate for content development because it separates research, drafting, and editing responsibilities.

## 3. Execution Evidence, Differences, and Problems

I executed both versions using the same command:

`uv run python main.py "Machine Learning in Healthcare"`

**Run 1 — Original Writer Backstory**

The original workflow completed successfully. The Research Analyst generated five key facts and two open questions. The Technical Writer produced a multi-paragraph explanation discussing clinical decision support, predictive risk modeling, drug discovery, regulation, and healthcare equity.

The Editor reorganized this material into a structured research brief with headings and `[source needed]` markers. The final output was informative, but it retained technical terminology such as AUC and electronic health records.

**Run 2 — Modified Writer Backstory**

The modified workflow also completed successfully. The Technical Writer began with a simpler explanation, comparing machine learning to learning from experience. However, its response stopped after an incomplete sentence: "these programs help doctors make".

Despite this incomplete draft, the Editor continued and generated a final research-oriented document. The final output included technical language and research findings rather than consistently addressing a 10-year-old audience.

**Comparison and Interpretation**

Run 2 showed an initial shift toward simpler language, but the change was not consistently preserved in the final output. The experiment therefore did not demonstrate an overall improvement in age-appropriate readability.

This suggests that changing one agent's backstory may influence its immediate response without guaranteeing the intended result across the entire multi-agent workflow. The Editor's unchanged instructions and the incomplete Writer output may have contributed to the outcome. Since language-model outputs vary between runs, a single comparison cannot isolate the exact cause.

**Troubleshooting**

Initially, the workflow failed when the Technical Writer received an empty language-model response. Changing the Groq model alone did not resolve the issue. After adding `max_completion_tokens=4096` to the LLM configuration, the original workflow completed successfully. Both successful runs were saved for comparison.

The generated healthcare claims were not independently verified. The starter kit does not provide its Research Analyst with web-search tools, so source labels and numerical claims should not be treated as confirmed evidence.

## 4. Framework Choice and Comparison

I chose CrewAI because the assignment involves multiple specialized roles working toward one shared result. Researching, drafting, and editing are naturally separate responsibilities, making CrewAI's agent-and-task structure a good fit.

CrewAI provides a straightforward way to define each agent's role, goal, and backstory. Tasks specify what each agent should produce, while the sequential process manages their execution order. This made it possible to experiment with one agent's instructions without redesigning the workflow.

An alternative framework is **LangGraph**, which represents workflows using states, nodes, and edges. LangGraph provides more explicit control over execution paths, conditional transitions, loops, and checkpoints. For example, a LangGraph implementation could check whether the Writer produced a complete explanation before allowing the Editor to continue.

CrewAI was simpler for this experiment because the workflow followed a predictable research-to-writing-to-editing sequence. However, the incomplete Writer response in Run 2 revealed a limitation of relying on sequential execution without an explicit output-quality check.

For a small collaborative writing workflow, I would choose CrewAI because its role-based design is intuitive. For a system requiring strict validation, conditional retries, or human approval, I would consider LangGraph because its execution flow can be controlled more precisely.

## 5. Verification and AI Use Disclosure

I installed the CrewAI dependencies using `uv sync` and verified the environment with `uv run python check_setup.py`, which returned `CrewAI kit OK`.

I then executed the original and modified workflows using the same healthcare topic. Both successful runs displayed `Crew Execution Completed`, and their terminal outputs and generated explanations were preserved in separate submission files.

The submitted `main.py` contains the modified Technical Writer backstory and the LLM completion-budget adjustment.

**AI Use Disclosure:** I used ChatGPT to help interpret the starter guide, troubleshoot CrewAI execution errors, identify a suitable backstory modification, and organize this report. The workflow was executed locally, and the observations in this report are based on the saved terminal logs and generated outputs. I reviewed the results and retained unsuccessful or unexpected behavior in the discussion rather than presenting the experiment as an unqualified success.

## Submission Evidence

- `main.py` — Modified CrewAI implementation
- `run1_original.txt` — Original successful execution log
- `run2_changed.txt` — Modified successful execution log
- `explainer_run1.md` — Original generated explanation
- `explainer_run2.md` — Modified generated explanation
- `REPORT.md` — Build report and comparison
