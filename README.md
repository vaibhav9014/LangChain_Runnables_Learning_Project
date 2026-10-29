# LangChain Runnables Learning Project

A hands-on Python project for learning LangChain Runnables, including sequential chains, parallel execution, custom functions, input passthrough, and conditional branching.

![Python](https://img.shields.io/badge/Python-3.9%2B-blue)
![LangChain](https://img.shields.io/badge/LangChain-Runnables-green)
![OpenRouter](https://img.shields.io/badge/LLM-OpenRouter-orange)
![License](https://img.shields.io/badge/License-MIT-lightgrey)

## Live Demo

This is a local command-line learning project. Run any script after setting the required environment variables:

```bash
python runnable_sequence.py
```

## Screenshots / GIF

No screenshots are included in this repository.

## Table of Contents

- [Project Goal](#project-goal)
- [Problem Statement](#problem-statement)
- [What It Does](#what-it-does)
- [Features](#features)
- [How It Works](#how-it-works)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [Environment Variables](#environment-variables)
- [Usage](#usage)
- [API Documentation](#api-documentation)
- [Project Structure](#project-structure)
- [Example Output](#example-output)
- [Testing](#testing)
- [Current Status](#current-status)
- [Milestones](#milestones)
- [Roadmap](#roadmap)
- [Known Limitations](#known-limitations)
- [Data and Privacy](#data-and-privacy)
- [Deployment](#deployment)
- [Contributing](#contributing)
- [License](#license)
- [Connect](#connect)
- [Acknowledgments](#acknowledgments)

## Project Goal

The goal of this project is to understand LangChain Runnables as a standardized way to build modular, composable LLM applications.

It demonstrates how prompts, language models, output parsers, and custom Python functions can be connected into complete workflows.

## Problem Statement

While building LLM applications, developers often need to combine multiple steps: generate content, process its output, run tasks in parallel, pass values through unchanged, or choose a path based on a condition.

Without a standard interface, each component may require custom glue code. LangChain Runnables solve this by giving every component a common interface and making components easy to compose.

## What It Does

This project contains several small scripts that demonstrate the main Runnable primitives:

- Generate a joke and explain it using a sequential chain.
- Generate a joke and calculate its word count in parallel.
- Generate a tweet and a LinkedIn post simultaneously.
- Pass an intermediate value through a chain without modifying it.
- Conditionally summarize a generated report only when it contains more than 300 words.
- Use a custom Python function inside a Runnable workflow.

## Features

- Standardized Runnable interface using `invoke`, `batch`, and `stream`.
- Sequential chaining with `RunnableSequence`.
- Concurrent execution with `RunnableParallel`.
- Custom Python logic with `RunnableLambda`.
- Value forwarding with `RunnablePassthrough`.
- Conditional execution with `RunnableBranch`.
- Prompt templates, chat model integration, and string output parsing.
- Environment-based API key configuration using `python-dotenv`.

## How It Works

A `Runnable` is a unit of work that can be invoked, batched, streamed, transformed, and composed. The core methods are:

| Method | Purpose |
|---|---|
| `invoke` | Takes one input and returns one output. |
| `ainvoke` | Asynchronous version of `invoke`. |
| `batch` | Processes a list of inputs efficiently, commonly in parallel. |
| `abatch` | Asynchronous version of `batch`. |
| `stream` | Streams output chunks incrementally for a single input. |
| `astream` | Asynchronous version of `stream`. |

LangChain components can be connected using the `|` operator or explicitly using `RunnableSequence`. In a sequence, the output of one Runnable becomes the input of the next Runnable.

For example:

```python
chain = prompt | model | parser
```

Here, the prompt formats the input, the model generates a response, and the parser converts the model message into a plain string.

## Architecture

The project uses five core Runnable composition primitives.

| Runnable | Purpose |
|---|---|
| `RunnableSequence` | Runs Runnables one after another. The output of the previous step becomes the input of the next step. |
| `RunnableParallel` | Runs multiple Runnables at the same time using the same input and returns a dictionary of results. |
| `RunnableLambda` | Converts a normal Python function into a Runnable. |
| `RunnablePassthrough` | Returns the input unchanged. It is useful for preserving an intermediate value. |
| `RunnableBranch` | Selects and runs one branch based on conditions. If no condition matches, it can run a default branch. |

### RunnableSequence Example

```python
joke_gen_chain = RunnableSequence(prompt1, model, parser)
```

Flow:

```text
Input -> Prompt -> Chat Model -> Output Parser -> Final Output
```

### RunnableParallel Example

```python
parallel_chain = RunnableParallel({
    "tweet": RunnableSequence(prompt1, model, parser),
    "linkedin": RunnableSequence(prompt2, model, parser)
})
```

Flow:

```text
                 -> Tweet Chain -> Tweet
Input -> Parallel
                 -> LinkedIn Chain -> LinkedIn Post
```

### RunnableLambda Example

```python
parallel_chain = RunnableParallel({
    "joke": RunnablePassthrough(),
    "word_count": RunnableLambda(word_count)
})
```

The original joke is preserved using `RunnablePassthrough`, while `RunnableLambda(word_count)` calculates the number of words.

### RunnablePassthrough Example

```python
parallel_chain = RunnableParallel({
    "joke": RunnablePassthrough(),
    "explanation": RunnableSequence(prompt2, model, parser)
})
```

The joke is returned directly, while the same joke is also sent to another chain for explanation.

### RunnableBranch Example

```python
branch_chain = RunnableBranch(
    (lambda x: len(x.split()) > 300, prompt2 | model | parser),
    RunnablePassthrough()
)
```

If the generated report has more than 300 words, it is summarized. Otherwise, the original report is returned unchanged.

## Tech Stack

- Python
- LangChain Core
- LangChain OpenRouter integration
- OpenRouter-hosted LLMs
- python-dotenv
- Git and GitHub

## Prerequisites

- Python 3.9 or later
- pip
- An OpenRouter API key
- Git

## Quick Start

1. Clone the repository:

```bash
git clone [https://github.com/](https://github.com/)<your-username>/<your-repository-name>.git
cd <your-repository-name>
```

2. Create and activate a virtual environment:

```bash
python -m venv .venv
```

On Windows:

```bash
.venv\Scripts\activate
```

On macOS and Linux:

```bash
source .venv/bin/activate
```

3. Install dependencies:

```bash
pip install -r requirements.txt
```

If `requirements.txt` is not present, install the required packages manually:

```bash
pip install langchain-core langchain-openrouter python-dotenv
```

4. Create a `.env` file:

```bash
touch .env
```

5. Add your OpenRouter API key:

```env
OPENROUTER_API_KEY=your_openrouter_api_key_here
```

6. Run a script:

```bash
python runnable_sequence.py
```

## Environment Variables

| Variable | Description | Required |
|---|---|---|
| `OPENROUTER_API_KEY` | API key used to authenticate requests to OpenRouter. | Yes |

Do not commit the `.env` file to GitHub. It should already be listed in `.gitignore`.

## Usage

Run each learning script individually:

```bash
python runnable_sequence.py
```

```bash
python runnable_parallel.py
```

```bash
python runnable_lambda.py
```

```bash
python runnable_passthrough.py
```

```bash
python runnable_branch.py
```

Each script sends a prompt to the configured OpenRouter model and prints the generated result in the terminal.

## API Documentation

This project does not expose a web API. It uses LangChain Runnables directly in Python.

### Main Runnable Methods

```python
result = chain.invoke({"topic": "AI"})
```

Runs the chain once with a single input.

```python
results = chain.batch([
    {"topic": "AI"},
    {"topic": "Cricket"}
])
```

Runs the chain for multiple inputs.

```python
for chunk in chain.stream({"topic": "AI"}):
    print(chunk, end="", flush=True)
```

Streams the output incrementally.

## Project Structure

```text
.
├── runnable_branch.py
├── runnable_lambda.py
├── runnable_parallel.py
├── runnable_passthrough.py
├── runnable_sequence.py
├── .env
├── .gitignore
└── README.md
```

## Example Output

For `runnable_sequence.py`:

```text
Why did the computer go to therapy?
Because it had too many bytes of emotional baggage.
```

For `runnable_parallel.py`:

```text
Why did the AI cross the road?
To optimize the other side!

Tweet: AI is changing the way we work, create, and solve problems.
LinkedIn: Artificial Intelligence is no longer just a future concept...
```

Actual output depends on the model response.

## Testing

This project is intended for learning and manual testing.

Run each script and verify that:

- The OpenRouter API key is loaded correctly.
- The script completes without import or authentication errors.
- The expected chain output appears in the terminal.
- Parallel chains return results for every branch.
- The branch condition works correctly in `runnable_branch.py`.

## Current Status

Completed:

- RunnableSequence
- RunnableParallel
- RunnableLambda
- RunnablePassthrough
- RunnableBranch
- Basic prompt, model, and output parser integration

## Milestones

- [x] Learn the Runnable interface.
- [x] Implement `invoke`, `batch`, and `stream` concepts.
- [x] Build sequential chains.
- [x] Build parallel chains.
- [x] Use custom Python functions with `RunnableLambda`.
- [x] Preserve intermediate values with `RunnablePassthrough`.
- [x] Implement conditional logic with `RunnableBranch`.
- [ ] Add structured output parsing.
- [ ] Add error handling and retries.
- [ ] Add unit tests.
- [ ] Add a simple web interface.

## Roadmap

- Add examples using async Runnables such as `ainvoke`, `abatch`, and `astream`.
- Add logging and tracing for chain execution.
- Add retry and fallback logic for API failures.
- Add structured output using Pydantic models.
- Add retrieval-augmented generation examples.
- Create a Streamlit or FastAPI demo.

## Known Limitations

- The project depends on an external OpenRouter API and requires an active API key.
- Model responses are non-deterministic, so outputs may differ between runs.
- The scripts are designed for learning, not production use.
- There is no automated test suite yet.
- The report summarization branch uses a simple word-count condition.

## Data and Privacy

Prompts and generated outputs are sent to the configured OpenRouter model provider. Do not enter personal, confidential, or sensitive information into prompts.

The `.env` file contains the API key and must not be uploaded to GitHub.

## Deployment

This project is designed to run locally.

For cloud deployment, store the OpenRouter API key as a secret environment variable and ensure that `.env` is not included in the deployment artifact.

Example:

```bash
export OPENROUTER_API_KEY=your_openrouter_api_key_here
python runnable_sequence.py
```

## Contributing

Contributions are welcome.

1. Fork the repository.
2. Create a new branch:

```bash
git checkout -b feature/your-feature
```

3. Make your changes.
4. Commit the changes:

```bash
git commit -m "Add your feature"
```

5. Push the branch:

```bash
git push origin feature/your-feature
```

6. Open a pull request.

## License

This project is licensed under the MIT License.

## Connect

Created by Vaibhav Yadav.

- GitHub: https://github.com/<vaibhav9014>
- LinkedIn: https://www.linkedin.com/in/<vaibhav-singh-69681a228/>

## Acknowledgments

- LangChain documentation and LangChain Core Runnable reference.
- CampusX LangChain learning playlist.
- OpenRouter for providing access to hosted language models.# LangChain_Runnables_Learning_Project
