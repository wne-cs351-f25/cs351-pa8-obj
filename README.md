# CS351 Programming Languages - Fall 2025

This is a template repository for CS351 Programming Languages course (Fall 2025). It provides a pre-configured development environment with the [PLCC (Programming Language Compiler Compiler)](https://github.com/ourPLCC/plcc) tool for building and experimenting with programming language implementations.

## Features

- Pre-installed PLCC tool for language design and implementation
- Configured development container for consistent environment across platforms
- Ready-to-use setup for course assignments and projects
- Integrated agentic learning assistant with [Claude Code](https://claude.ai/code), [Gemini CLI](https://ai.google.dev/gemini-api/docs/cli), [GitHub Copilot CLI](https://github.com/github/gh-copilot), or [OpenAI Codex](https://openai.com/index/openai-codex/) for guided PLCC development

## Getting Started

1. Fork or use this template repository
2. Open in a development container-supported environment ([VS Code with Dev Containers extension](https://code.visualstudio.com/docs/devcontainers/containers), [GitHub Codespaces](https://github.com/features/codespaces), [DevPod](https://devpod.sh/), etc.)
3. Start building your programming language implementations

## Agentic Learning Assistant

This environment includes an integrated learning assistant that helps guide your PLCC development process. The assistant follows specific pedagogical guidelines to help you learn programming language concepts through hands-on discovery.

### Key Features

- **Guided Discovery Learning**: The assistant asks leading questions rather than providing complete solutions
- **PLCC Workflow Support**: Understands the `plccmk`, `scan`, `parse`, and `rep` command workflow
- **Grammar Development Help**: Assists with lexical, syntactic, and semantic specification development
- **Debugging Guidance**: Helps you systematically identify and resolve issues in your language implementations

### Supported Ruler Instructions

The environment is pre-configured with ruler instructions that enable the assistant to:

- Explain PLCC grammar file structure and syntax
- Guide token definition and regex pattern development
- Help debug scanner, parser, and interpreter issues
- Suggest systematic testing approaches using sample programs
- Provide conceptual explanations of formal language theory
- Support the three-phase development workflow (lexing → parsing → interpreting)

### Usage Tips

- Ask conceptual questions about language design decisions
- Request guidance on debugging specific PLCC errors
- Seek help understanding grammar file sections and their interactions
- Get assistance with systematic testing strategies for your language implementations

## License

See [LICENSE.md](LICENSE.md) for licensing information.
