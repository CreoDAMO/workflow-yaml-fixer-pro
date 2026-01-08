# Workflow YAML Fixer Pro

**Advanced GitHub Actions workflow YAML editor, fixer, validator, dependency generator, and visualizer — all in one standalone tool.**

<div align="center">

[![GitHub Pages](https://img.shields.io/badge/demo-live-brightgreen)](https://CreoDAMO.github.io/workflow-yaml-fixer-pro/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![GitHub Stars](https://img.shields.io/github/stars/CreoDAMO/workflow-yaml-fixer-pro)](https://github.com/CreoDAMO/workflow-yaml-fixer-pro/stargazers)
[![GitHub Issues](https://img.shields.io/github/issues/CreoDAMO/workflow-yaml-fixer-pro)](https://github.com/CreoDAMO/workflow-yaml-fixer-pro/issues)

### 👉 **[Try Live Demo](https://CreoDAMO.github.io/workflow-yaml-fixer-pro/)** 👈

*Everything runs in your browser — no data leaves your machine.*

</div>

---

## ✨ Features

- **Intelligent YAML Fixer** – Auto-detects and corrects syntax errors, indentation issues, and common GitHub Actions mistakes
- **Context-Aware Fixing** – Paste GitHub error messages for targeted corrections
- **Best-Practice Validation** – Checks permissions, concurrency, action pinning, security patterns, and more
- **Dependency Generator** – Extracts and creates `requirements.txt`, `package.json`, `pom.xml`, `go.mod`, etc.
- **Interactive Workflow Graph** – Visualizes job dependencies and execution flow
- **Professional Templates** – Ready-to-use CI/CD pipelines for Node.js, Python, Docker, AWS, and more
- **Fully Offline-Capable** – Works without internet after first load
- **Privacy-First** – No tracking, client-side only processing

---

## 🚀 Quick Start

### Option 1: Live Demo (Recommended)

Visit **https://CreoDAMO.github.io/workflow-yaml-fixer-pro/** — no installation needed.

### Option 2: Local Use

```bash
git clone https://github.com/CreoDAMO/workflow-yaml-fixer-pro.git
cd workflow-yaml-fixer-pro
```

Then open `index.html` in your browser:

```bash
# macOS
open index.html

# Linux
xdg-open index.html

# Windows
start index.html
```

Or run a local server:

```bash
python -m http.server 8000
# Visit http://localhost:8000
```

---

## 📋 Usage

1. **Paste your workflow YAML** into the editor
2. **Click "Fix YAML"** to auto-correct common issues
3. **Review validation warnings** for best practices
4. **Generate dependencies** if your workflow uses package managers
5. **Visualize job flow** to understand execution order
6. **Export or copy** the corrected YAML

### Fixing with Error Messages

If GitHub Actions gives you an error, paste it into the tool for context-aware fixes:

```
Error: Invalid workflow file
Line 15: 'runs-on' is required
```

The tool will identify and fix the specific issue.

---

## 🛠️ Latest Action Versions

The tool includes up-to-date versions of popular GitHub Actions (as of January 2026):

| Action | Version |
|--------|---------|
| `actions/checkout` | v6.0.1 |
| `actions/setup-node` | v6.1.0 |
| `actions/setup-python` | v6.1.0 |
| `actions/setup-java` | v5.1.0 |
| `actions/upload-artifact` | v6.0.0 |
| `actions/download-artifact` | v7.0.0 |
| `actions/cache` | v5.0.1 |
| `docker/login-action` | v3.6.0 |
| `aws-actions/configure-aws-credentials` | v5.1.1 |

The tool can also fetch the absolute latest versions via the GitHub API.

---

## 🤝 Contributing

We welcome contributions! You can help by:

- Reporting bugs or requesting features via [Issues](https://github.com/CreoDAMO/workflow-yaml-fixer-pro/issues)
- Submitting pull requests for new templates, validation rules, or UI improvements
- Improving documentation

See [CONTRIBUTING.md](CONTRIBUTING.md) for detailed guidelines.

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

### Commercial Use

The MIT License allows free use for personal, open-source, and internal company projects. For organizations needing additional rights (white-labeling, embedding in proprietary products, priority support), commercial licenses are available.

📧 Contact: commercial@workflowfixer.com  
📖 Details: [COMMERCIAL-LICENSE.md](COMMERCIAL-LICENSE.md)

---

## 🌟 Support the Project

If you find this tool helpful:

- ⭐ [Star the repository](https://github.com/CreoDAMO/workflow-yaml-fixer-pro/stargazers)
- 🐛 [Report issues](https://github.com/CreoDAMO/workflow-yaml-fixer-pro/issues)
- 💬 [Join discussions](https://github.com/CreoDAMO/workflow-yaml-fixer-pro/discussions)
- 🔀 Share with your team

---

<div align="center">

Made with ❤️ for the GitHub Actions community

</div>
