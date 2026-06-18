# Job Fit Engine

`job-fit-engine` is a rule-based Python project that evaluates job descriptions against a structured career-fit rubric.

It turns a qualitative decision into a repeatable, explainable scorecard by checking:

- eligibility
- stretch risk
- role sustainability and support fit
- long-term narrative fit

The project includes:

- a Python scoring engine
- a CLI for fast text-file or stdin evaluation
- a Streamlit app for interactive review
- unit tests for representative good-fit, borderline, and reject scenarios

## Demo

[Try the Streamlit app](https://job-fit-engine-v1-iedx532y2tvwdbuo59edcf.streamlit.app/)

<img width="655" height="628" alt="image" src="https://github.com/user-attachments/assets/7514b72d-2c29-4e9e-98c3-ff988b3ec058" />
<img width="646" height="842" alt="image" src="https://github.com/user-attachments/assets/3fd0d363-1cb6-44da-be47-82fc52e31579" />
<img width="632" height="851" alt="image" src="https://github.com/user-attachments/assets/bbbab7fa-5b44-4529-8e79-a10f05e29ee3" />
<img width="627" height="511" alt="image" src="https://github.com/user-attachments/assets/2c80f68e-2554-4925-a95a-a6bc91ab933f" />

## Why I Built It

Job-search decisions are often subjective, inconsistent, and hard to explain after the fact. I designed this project to make that process more structured and repeatable.

The main reason I created the engine was to help evaluate whether roles were genuinely aligned with my current skillset, strengths, and longer-term career direction. I wanted a way to separate roles that only looked relevant on the surface from roles where I could realistically contribute, develop, and build useful experience.

The scoring model is designed to identify roles that make good use of my strengths and help flag roles that may be misaligned with the type of work I want to grow into.

Rather than treating every interesting job description as equally worth applying for, the engine supports a more disciplined decision process: apply where there is a strong fit, be cautious where the role is a stretch, and reject roles where the risks outweigh the potential value. The goal is to support better application decisions, reduce wasted effort on roles that are unlikely to be a strong match, and make the decision process more consistent from one role to the next rather than dependent on day-to-day judgment.

Instead of relying on a vague sense of whether a role "seems right," the engine applies a fixed rubric and returns a transparent, explainable breakdown of why a role looks like a fit, a stretch, or a poor match.

## Authorship and AI Assistance

I designed the scoring rubric, category structure, candidate assumptions, decision criteria, and expected behaviours for this project.

The implementation was developed with support from Codex as a coding assistant as part of an AI-assisted implementation workflow. I used Codex to help translate the rubric into Python code, build the CLI and Streamlit interface, and generate or refine unit tests.

My main contribution was the system design, rubric design, decision modelling, and QA of outputs: defining what the engine should evaluate, how categories should be weighted, what should count as a green, amber, or red signal, and how the final verdict should be interpreted.

After the initial implementation, I manually reviewed and adjusted the keyword and phrase signals used across the scoring categories in Visual Studio Code. This helped reduce false positives and false negatives and ensured the outputs better matched the intended rubric logic.

I also reviewed and iterated on the outputs to ensure the tool reflected the intended decision logic.

## Highlights

- explainable rule-based scoring rather than black-box prediction
- 15 weighted categories covering fit, risk, support, and progression
- explicit eligibility checks tied to candidate constraints
- both CLI and Streamlit interfaces for different workflows
- test coverage for strong-fit, stretch, and reject scenarios

## What It Does

Given a job description, the engine:

1. checks eligibility signals
2. scores the role across 15 weighted categories
3. classifies the role as a supported, moderate, or high-risk stretch
4. flags critical red categories
5. returns a Track A verdict and, when relevant, a Track B fallback assessment

This is intentionally rule-based rather than ML-based. The goal is transparency and consistency, not black-box prediction.

## Tech Stack

- Python
- Streamlit
- `unittest`
- simple package/CLI wiring through `pyproject.toml`

## Scoring Model

The scoring rubric is defined in [specs/track_a_core_bridge.md](specs/track_a_core_bridge.md).

The current implementation evaluates:

- core career-path alignment
- technical systems fit
- barrier to entry
- day-1 usefulness
- stakeholder and collaboration load
- ambiguity level
- structure and predictability
- ramp-up realism
- internal systems vs external stakeholder context
- pressure and operational load
- work mode stability
- training and support
- narrative value
- support and working environment fit
- stability and progression

Each category is scored as `green`, `amber`, or `red`, then converted into a weighted score out of 100.

Roles that fail Track A can also be routed into a Track B fallback assessment, which helps separate roles that are less aligned for career development but still viable as a bridge into a more aligned role from roles that are not suitable.

## Eligibility Assumptions

The eligibility logic is currently tuned to an example UK-based early-career candidate profile.

That means the engine does more than generic role scoring. It also tries to detect eligibility constraints such as:

- student-only hiring language
- class-year restrictions
- recent-graduate windows
- graduate-level eligibility requirements

If you want to reuse the engine for a different profile, this is the main area to adapt.

## How It Works

At a high level, the workflow is:

1. clean and normalize the job description
2. detect explicit eligibility signals
3. evaluate 15 rubric categories using phrase and pattern matching
4. calculate a weighted score and critical red flags
5. classify stretch risk and produce a final verdict

The core engine lives in [job_fit_engine/engine.py](job_fit_engine/engine.py), with lightweight data models and separate CLI/UI entry points layered on top.

## Sample Input and Output

Sample input:

```text
Junior Data Quality Analyst role supporting internal data validation, structured workflows, Excel-based checks, SQL queries, and migration support. Includes clear onboarding, documented processes, and structured internal workflow support.
```

Example output:

```text
Total score: 78.5/100
Track A verdict: Apply (low confidence: limited input)
Stretch Risk: Supported stretch
Critical red flags: none

Strong signals:
- Internal-facing data quality work
- Structured workflows
- Clear onboarding/training signal
- QA and validation process fit

Watch points:
- Eligibility is unclear from the short sample text.
- Support level would need confirming from the full job description.
- The limited input means the verdict is lower-confidence.
```

## Project Structure

- [job_fit_engine/engine.py](job_fit_engine/engine.py): core rule engine and scoring logic
- [job_fit_engine/cli.py](job_fit_engine/cli.py): command-line entry point and formatted report output
- [job_fit_engine/models.py](job_fit_engine/models.py): result models
- [streamlit_app.py](streamlit_app.py): interactive Streamlit UI
- [tests/test_engine.py](tests/test_engine.py): unit coverage for expected scenarios
- [tests/anchors](tests/anchors): example inputs and expected outcomes

## Quick Start

### 1. Install the package

```powershell
python -m pip install -e .
```

If you want to use the Streamlit interface as well:

```powershell
python -m pip install -e ".[ui]"
```

### 2. Run the CLI

Evaluate inline text:

```powershell
job-fit-engine --text "Junior internal data quality role with structured workflows, training, and repeatable validation processes."
```

Evaluate a saved file:

```powershell
job-fit-engine --file .\role.txt
```

Evaluate from stdin:

```powershell
Get-Content .\role.txt | job-fit-engine
```

### 3. Run the Streamlit app

```powershell
streamlit run streamlit_app.py
```

Then paste a job description into the app to see:

- eligibility
- stretch classification
- total Track A score
- critical red flags
- per-category reasoning

## Example Outcome

For a structured junior internal data-quality role, the expected result is:

- high overall score, ideally 80+
- `Apply immediately` Track A verdict
- no critical red flags

For a senior client-facing analyst role with high ambiguity, pressure, and minimal onboarding, the expected result is:

- low overall score
- `Reject from Track A`
- multiple red support-fit or alignment signals

See the anchor files in [tests/anchors](tests/anchors) for sample descriptions and expected outcomes.

## Portfolio Value

This project is a good example of:

- designing a role-evaluation framework around skill alignment, development potential, and sustainability
- designing an evaluation rubric and encoding it in software
- translating qualitative job-fit judgement into explicit scoring rules
- directing an AI coding assistant to implement a clearly specified rubric and decision-support workflow
- manually refining keyword and phrase signals to improve scoring accuracy and reduce false positives and false negatives
- reviewing and refining outputs to ensure the implementation matched the intended decision logic
- building a small end-to-end tool with backend logic, CLI use, Streamlit UI, and tests
- writing tests around edge cases and decision consistency

It is less about "predicting the perfect job" and more about building a transparent decision-support system.

## What I Learned

This project helped me practise turning an ambiguous real-world decision process into explicit rules, categories, and testable outputs.

Key learning points included:

- defining scoring categories clearly enough to be implemented in code
- translating qualitative judgement into repeatable decision logic
- reviewing false positives and false negatives in rule-based outputs
- improving keyword and phrase matching so isolated words did not over-influence the final score
- using tests to check that strong-fit, stretch, and reject examples behaved as expected
- separating explainable scoring from black-box prediction
- documenting limitations honestly so the tool is useful without being overstated

## Testing

Run the test suite with:

```powershell
python -m unittest discover -s tests -p "test*.py"
```

The tests cover:

- strong Track A fits
- clear Track A rejects
- eligibility edge cases
- stretch classification behavior
- Track B fallback routing

## Design Principles

This project is designed around a few principles:

- transparent scoring over opaque prediction
- explicit reasoning over single-number output
- sustainable role fit, not just keyword matching
- clear career-path fit, not generic job relevance

## Limitations

- The rules are heuristic and intentionally opinionated.
- The engine is tuned to a specific career-fit profile rather than general-purpose job matching.
- It works best with complete job descriptions, not short summaries.
- It does not currently use machine learning or semantic parsing, so subtle context can still be missed.
- The scoring model depends on the quality of the rubric and phrase patterns.

## Future Improvements

- parameterise the configured candidate profile instead of hard-coding it
- export results as JSON or CSV
- add richer fixtures for real-world job-description formats
- separate rubric configuration from engine code
- improve section-aware parsing for responsibilities, requirements, and benefits
