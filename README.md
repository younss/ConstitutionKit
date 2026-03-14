# ConstitutionKit

**Generate production-ready engineering constitutions for AI-first projects.**

[**Try it live →**](https://younss.github.io/ConstitutionKit/)

ConstitutionKit is a browser-side tool that generates two files:

- `constitution.md` — foundational principles and behavioral contracts for your project, readable by humans and AI agents alike (used by [spec-kit](https://github.com/github/spec-kit))
- `CLAUDE.md` — agent-specific instructions for [Claude Code](https://docs.anthropic.com/en/docs/claude-code): allowed tools, forbidden paths, build commands, and autonomy boundaries

Everything runs in your browser. No account, no server, no data sent anywhere.

---

## Features

- **Project types** — Web App, Microservices, Data Pipeline, AI System, Platform/SDK, Mobile App, Flutter App
- **Principle library** — 50+ principles across 10 categories: Software Engineering, Testing, Error Handling, API Design, Security, Observability, CI/CD, Data, AI/LLM, Flutter & Dart
- **Presets** — AI-First Stack, Startup Speed, Enterprise Grade, Security-Critical, Flutter/Dart
- **Share config** — copy a URL that encodes your full configuration including project context, so teammates start with the same setup
- **Live preview** — toggle between raw Markdown and rendered preview; edit the output before downloading

## Getting started

Open [https://younss.github.io/ConstitutionKit/](https://younss.github.io/ConstitutionKit/) and follow the four steps:

1. **Choose your tool** — `constitution.md`, `CLAUDE.md`, or both
2. **Project type & stack** — select your project category and tech stack
3. **Select principles** — pick from presets or build your own set
4. **Project details** — add build commands, forbidden paths, autonomy level

Then click **Generate** and download your files.

## Local development

This is a single-file static site. No build step required.

```bash
git clone https://github.com/younss/ConstitutionKit.git
cd ConstitutionKit
# Open index.html in any browser, or serve with:
npx serve .
```

## Deployment

Deployed via GitHub Pages from the `main` branch. Any push to `main` updates the live site automatically.

## Contributing

Contributions welcome — especially new principles, project types, or preset configurations.

1. Fork the repository
2. Make your changes to `index.html`
3. Open a pull request with a description of what you added and why

## License

MIT — see [LICENSE](LICENSE) for details.
