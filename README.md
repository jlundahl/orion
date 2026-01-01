# Project ORION

**An AI Agent for Autonomous Document Creation**

Build a production-grade AI agent that creates spreadsheets, presentations, and Word documents—checking its own work along the way.

## What Is This?

ORION is a complete tutorial for building a basic autonomous agent from first principles. Give it a task in plain English like *"Create a spreadsheet with quarterly sales data"* and it will:

1. Write code to create the document
2. Execute that code
3. Convert the result to an image
4. Use vision AI to verify it looks correct
5. If something's wrong, fix it and try again
6. Return the finished file

This isn't a toy example—it's the same architecture used in production AI systems.

## Architecture

```
┌─────────────────────────────────────────────────────┐
│                 DOCKER CONTAINER                    │
│                                                     │
│   ┌──────────────┐     ┌────────────────────────┐   │
│   │    AGENT     │────▶│    ARTIFACT TOOLS      │   │
│   │   (Brain)    │     │   (Spreadsheet/PPT/    │   │
│   │              │     │    Document creation)  │   │
│   └──────────────┘     └────────────────────────┘   │
│          │                        │                 │
│          ▼                        ▼                 │
│   ┌──────────────┐     ┌────────────────────────┐   │
│   │  QA PIPELINE │     │  PRESENTATION SERVER   │   │
│   │   (Vision    │     │     (Node.js)          │   │
│   │  Verification)│    └────────────────────────┘   │
│   └──────────────┘                                  │
└─────────────────────────────────────────────────────┘
                         │
                         ▼
                ┌─────────────────┐
                │  ANTHROPIC API  │
                │    (Claude)     │
                └─────────────────┘
```

## Prerequisites

- **macOS** (guide written for Mac, adaptable to Linux)
- **Docker Desktop**
- **Anthropic API key** ([get one here](https://console.anthropic.com/settings/keys))

## Quick Start

### 1. Install Dependencies

```bash
# Install Homebrew (if needed)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install required tools
brew install --cask docker
brew install git protobuf python@3.11 node@22
```

### 2. Clone/Create the Project

Follow the full guide to create the project structure, or if you have the code:

```bash
cd orion-agent
```

### 3. Build the Docker Image

```bash
docker build -t orion-agent:1.0 .
```

### 4. Run ORION

```bash
export ANTHROPIC_API_KEY="sk-ant-your-key-here"

docker run --rm \
  -e ANTHROPIC_API_KEY="$ANTHROPIC_API_KEY" \
  -v "$(pwd)/output:/app/output" \
  -p 3000:3000 \
  orion-agent:1.0 \
  bash -c '
    node /app/js_pipeline/server.js &
    for i in $(seq 1 30); do
      curl -s http://localhost:3000/health > /dev/null 2>&1 && break
      sleep 1
    done
    python3 /app/src/agent/main.py "Create a spreadsheet with Q1-Q4 sales data"
  '
```

Your generated file will appear in the `output/` folder.

## Project Structure

```
orion-agent/
├── src/
│   ├── agent/           # Main orchestrator
│   ├── artifact_tool/   # Document creation tools
│   ├── proto/           # Protocol Buffer definitions
│   ├── generated/       # Generated protobuf code
│   ├── qa_pipeline/     # Vision-based verification
│   └── skills/          # AI instruction guides
├── js_pipeline/         # Node.js presentation server
├── tests/               # Automated tests
└── output/              # Generated documents
```

## Key Technologies

| Technology | Purpose |
|------------|---------|
| **Python** | Core agent logic and document creation |
| **Protocol Buffers** | Structured data definitions |
| **Docker** | Isolated, reproducible environment |
| **Node.js** | PowerPoint generation (via pptxgenjs) |
| **LibreOffice** | Document-to-PDF conversion |
| **Claude API** | Code generation and vision verification |

## Supported Document Types

- **Spreadsheets** (.xlsx) — with formula evaluation
- **Presentations** (.pptx) — via JavaScript pipeline
- **Documents** (.docx) — Word documents

## Running Tests

```bash
# In Docker
docker run --rm orion-agent:1.0 python3 -m pytest /app/tests/ -v

# Locally
source venv/bin/activate
export PYTHONPATH=$(pwd)
python -m pytest tests/ -v
```

## What You'll Learn

By building ORION, you'll understand:

- **Agent loops** — How autonomous AI systems iterate toward goals
- **Protocol Buffers** — Structured data across languages
- **Render-and-verify QA** — Vision models for quality assurance
- **Docker containerization** — Packaging complex systems
- **Multi-language architecture** — Python + JavaScript working together

## License

Educational project. See full guide for details.

---

*Built with Claude (Anthropic Edition, December 2025)*
