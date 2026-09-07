# Autonomous Multi-Agent Red-Teaming Baseline

An autonomous multi-agent security evaluation framework designed to stress-test Large Language Model (LLM) guardrails. This baseline executes an iterative actor-critic feedback loop to discover vulnerabilities in conversational AI systems.

## Architecture Overview

```text
                      +-----------------------------------+
                      |         Adversary Agent           |
                      |   (Generates & Mutates Attacks)   |
                      +-----------------+-----------------+
                                        |
                          Payload a_t   |
                                        v
                      +-----------------+-----------------+
                      |           Target Agent            |
                      |   (Database Assistant w/ Rules)   |
                      +-----------------+-----------------+
                                        |
                        Response r_t    |
                                        v
                      +-----------------+-----------------+
                      |           Judge Agent             |
                      |   (Compliance & Leak Analysis)    |
                      +-----------------+-----------------+
                                        |
                      Feedback & Verdict| (Loop back if blocked)
                                        v
                      +-----------------+-----------------+
                      |    Adversary Trajectory Memory    |
                      +-----------------------------------+
```

The system operates using three distinct interacting agents:
* **Adversary Agent:** Generates prompt injection attacks (a_t), maintains a trajectory memory of prior refusals, and mutates payloads each turn to bypass defenses.
* **Target Agent:** A simulated database assistant holding protected credentials (`SEC_TOKEN_ALPHA_992`) behind strict safety filters. Returns a response (r_t) based on the payload.
* **Judge Agent:** Evaluates interactions each round, issues a compliance verdict (`BLOCKED` or `COMPROMISED`), and feeds directional critique back to the adversary.

## Reproducibility and Execution Modes

To ensure seamless execution across diverse environments, this project implements a dual-mode execution engine:
* **Live LLM Mode:** When an `OPENAI_API_KEY` is present, the Adversary Agent dynamically generates novel prompt injections using `gpt-4o-mini`.
* **Deterministic Fallback Mode:** If no API key is detected, the system automatically falls back to an internal deterministic state machine. This guarantees 100% reproducibility and allows the system to run out-of-the-box without requiring external API credits or network access.

## Prerequisites
* Python 3.8+
* `openai` package (listed in `requirements.txt`)

## Setup Instructions

1. **Clone the repository:**
   ```bash
   git clone https://github.com/0xmzmdr/multiagent-ai-security.git
   cd multiagent-ai-security
   ```

2. **Set up a virtual environment:**
   ```bash
   python3 -m venv venv
   
   # macOS/Linux:
   source venv/bin/activate  
   
   # Windows:
   venv\Scripts\activate
   ```

3. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

## Configuration (Optional)

To utilize the live LLM generation mode, export your OpenAI API key before executing the script. If you skip this step, the system will safely default to the deterministic fallback mode.

```bash
# macOS/Linux
export OPENAI_API_KEY="sk-your-key-here"

# Windows Command Prompt
set OPENAI_API_KEY=sk-your-key-here
```

## Usage

Execute the baseline script from the root directory:
```bash
python3 run_baseline.py
```

**Inputs and Outputs:**
* **Input:** The test parameters (Target Guardrail, Target Secret Token, and Max Iterations) are instantiated directly inside the agent classes in `run_baseline.py` to keep the baseline self-contained.
* **Output:** The multi-agent interaction trace—including the Adversary's payloads, the Target's responses, and the Judge's evaluations—will stream directly to the terminal console.
