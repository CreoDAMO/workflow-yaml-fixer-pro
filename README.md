# workflow-yaml-fixer-pro
"Advanced GitHub Actions workflow YAML fixer with validation, dependencies generation, and visualization"    - **Visibility**: Choose "Public"

[image_0]: https://pfst.cf2.poecdn.net/base/image/30d0e89330ceda38432d352da99cc891258cc6c16893637656bc9a98176b5ec5?pmaid=544586084
[image_1]: https://pfst.cf2.poecdn.net/base/image/f3b304f57c2426421a4fc7228bc43c458f37a4fcfd1fce232548680a40064d58?pmaid=544586085
[image_2]: https://pfst.cf2.poecdn.net/base/image/014ae1a1c797fbe8aed1a36aca31dc08e50418d6e0a91ec0f73bbd483151fcf0?pmaid=544586089
[image_3]: https://pfst.cf2.poecdn.net/base/image/5f561d3fad59800db2617299e18edceb4faa19ce83ff34e87f8f62dc72bcbc1c?pmaid=544586094

<div align="center">

**Advanced error detection, debugging, validation, and dependency generation for GitHub Actions workflows**

[![GitHub Pages][image_0]](https://CreoDAMO.github.io/workflow-yaml-fixer-pro)
[![License: MIT][image_1]](LICENSE)
[![GitHub Stars][image_2]](https://github.com/CreoDAMO/workflow-yaml-fixer-pro/stargazers)
[![GitHub Issues][image_3]](https://github.com/CreoDAMO/workflow-yaml-fixer-pro/issues)

[Workflow YAML Fixer Pro](https://via.placeholder.com/1000x300/667eea/ffffff?text=Workflow+YAML+Fixer+Pro)

*Advanced error detection, validation, and dependency generation for GitHub Actions workflows*

</div>

---

## ✨ Features

Workflow YAML Fixer Pro is a comprehensive, all-in-one solution for managing GitHub Actions workflow files. Whether you're troubleshooting syntax errors, validating best practices, or generating dependency files, this tool has you covered.

### 🔧 YAML Fixer

The core functionality that started it all. Our intelligent YAML fixer analyzes your workflow files and automatically detects and corrects common issues.

**Key Capabilities:**

- **Intelligent Error Detection**: Automatically identifies syntax errors, indentation issues, and malformed YAML structures
- **Targeted Fixes**: Paste GitHub Actions error messages directly into the tool for context-aware corrections
- **Debug Analysis**: Provides step-by-step explanations of what was fixed and why
- **Support for All GitHub Actions Patterns**: Handles jobs, steps, runs-on, uses, runs, needs, if conditions, and more
- **Real-time Preview**: See the original and fixed YAML side-by-side with clear highlighting of changes

The fixer recognizes common GitHub Actions error patterns including:
- "Unexpected value" errors
- "Mapping values are not allowed here"
- "Could not find expected ':'"
- "Did not find expected key"
- Duplicate key detection
- Invalid indentation at any nesting level

### ✅ Validation Tab

Ensure your workflows follow GitHub Actions best practices with our comprehensive validation system.

**Validation Checks Include:**

- **Required Fields Verification**: Confirms presence of name, on trigger, and jobs sections
- **Action Version Analysis**: Identifies deprecated or unpinned action versions (e.g., @v1, @v2, @main)
- **Security Audits**: Warns about secret usage in run commands and recommends secure patterns
- **Concurrency Checks**: Validates concurrency configuration to prevent redundant workflow runs
- **Permission Reviews**: Checks for explicit permissions following the principle of least privilege
- **Matrix Strategy Validation**: Ensures matrix configurations are properly formatted
- **Cron Expression Validation**: Validates scheduled workflow syntax

**Actionable Recommendations:**

Each validation result includes specific guidance for improvement, such as:
- Pinning actions to specific versions (@v4.1.0 instead of @main)
- Adding missing concurrency groups
- Setting appropriate permissions
- Using timeout-minutes for long-running jobs
- Implementing proper conditionals (if) for conditional execution

### 📦 Dependencies Generator

Automatically extract and generate dependency files from your workflow configurations. This feature saves hours of manual work when setting up new development environments or migrating workflows.

**Supported Dependency Types:**

| Language/Platform | Generated Files | Detection Patterns |
|-------------------|-----------------|-------------------|
| **Python** | `requirements.txt` | `pip install` commands |
| **Node.js** | `package.json` | `npm install`, `yarn add` commands |
| **Java/Maven** | `pom.xml` | `mvn` commands, `pom.xml` references |
| **Go** | `go.mod` | `go mod` commands, `go.mod` references |
| **Ruby** | `Gemfile` | `bundle` commands, `Gemfile` references |
| **Docker** | `docker-compose.yml` | `docker run`, `image:` references |
| **System** | `packages.txt` | `apt-get install`, `yum install` commands |

**Features:**

- Extract packages from install commands automatically
- Generate proper `package.json` with dependency objects
- Create Docker Compose files from image references
- Support for multiple dependency types in a single workflow
- Export all dependencies as a JSON bundle for backup or migration

### 📋 Templates Tab

Jumpstart your workflow development with pre-built templates that follow industry best practices.

**Available Templates:**

1. **🟢 Node.js CI**: Complete continuous integration pipeline for Node.js projects
   - Multi-version testing (Node 18, 20, 22)
   - npm caching for faster builds
   - Linting and test execution
   - Matrix strategy for comprehensive coverage

2. **🐍 Python CI**: Python project CI with pytest and coverage reporting
   - Multi-version Python testing (3.10, 3.11, 3.12)
   - pip caching optimization
   - Flake8 linting integration
   - pytest with coverage XML output

3. **🐳 Docker Build & Push**: Container image build and publish workflow
   - Docker Buildx setup
   - GitHub Container Registry integration
   - Multi-tag support (latest, sha, branch, commit)
   - GitHub Actions cache integration

4. **🚀 Deploy to AWS**: Production deployment workflow for AWS
   - AWS credentials configuration
   - S3 sync with CloudFront invalidation
   - Environment protection rules
   - Manual approval workflow support

5. **🔒 Security Scan**: Comprehensive security scanning pipeline
   - Dependency Review Action
   - CodeQL analysis
   - Trivy vulnerability scanning
   - SARIF output for GitHub Security tab

6. **🔀 Multi-Stage Pipeline**: Complete CI/CD with lint, test, build, and deploy
   - Sequential job dependencies (needs)
   - Multi-OS testing matrix
   - Artifact upload/download between jobs
   - Production environment protection

Each template is fully documented with inline comments explaining key configuration choices.

### 🎨 Workflow Preview Tab

Visualize your workflow structure with an intuitive diagram that shows job relationships and step breakdowns.

**Visualization Features:**

- **Job Dependency Graph**: See how jobs flow into each other with the `needs` keyword
- **Parallel vs Sequential Detection**: Automatically identifies which jobs run in parallel vs sequentially
- **Step Breakdown**: View all steps within each job with action names
- **Trigger Visualization**: See what events trigger the workflow (push, pull_request, schedule)
- **Export Capability**: Visual diagrams help team members understand complex workflows at a glance

---

## 🚀 Getting Started

### Option 1: GitHub Pages (Recommended)

The easiest way to use Workflow YAML Fixer Pro is through our live GitHub Pages deployment:

**👉 [https://CreoDAMO.github.io/workflow-yaml-fixer-pro/](https://CreoDAMO.github.io/workflow-yaml-fixer-pro/)**

Simply open the link in your browser—no installation required. The tool works entirely client-side, so your YAML data never leaves your browser.

### Option 2: Local Development

For development or offline use, clone the repository and open the HTML file:

```bash
# Clone the repository
git clone https://github.com/CreoDAMO/workflow-yaml-fixer-pro.git

# Navigate to the project directory
cd workflow-yaml-fixer-pro

# Open index.html in your default browser
# On macOS:
open index.html

# On Linux:
xdg-open index.html

# On Windows:
start index.html
```

Or serve it with a local development server:

```bash
# Using Python
python -m http.server 8000

# Using Node.js (requires http-server)
npx http-server

# Using PHP
php -S localhost:8000
```

Then visit `http://localhost:8000` in your browser.

### Option 3: npm/Node.js Development

If you want to contribute to the project or modify the source code:

```bash
# Clone and enter directory
git clone https://github.com/CreoDAMO/workflow-yaml-fixer-pro.git
cd workflow-yaml-fixer-pro

# Install development dependencies (if any)
npm install

# Start development server with live reload
npm run dev
```

---

## 📖 Usage Guide

### Quick Start

1. **Open the tool** via GitHub Pages or local installation
2. **Paste your workflow YAML** into the "Original YAML" input area
3. **(Optional) Paste GitHub Actions error message** for targeted fixes
4. **Click "Fix YAML"** to process and view results

### Advanced Features

#### Targeted Error Fixing

When you encounter an error in GitHub Actions, copy the error message and paste it into the error input section. The fixer uses this context to provide more accurate fixes:

```
Example error message:
".github/workflows/deploy.yml (Line: 15, Col: 3): Unexpected value"
```

#### Validation Workflow

1. Navigate to the **Validation** tab
2. Click **Validate YAML** to run comprehensive checks
3. Review warnings, errors, and info messages
4. Click **Auto-Fix All Issues** to apply common fixes automatically
5. Return to the Fixer tab to apply indentation corrections

#### Dependency Generation

1. Paste a workflow with dependency installation commands
2. Switch to the **Dependencies** tab
3. Click **Generate All Dependencies**
4. View, copy, or download generated files
5. Use **Download All** to get a JSON bundle of all dependencies

#### Using Templates

1. Go to the **Templates** tab
2. Browse available templates with descriptions and tags
3. Click any template card to load it into the editor
4. Customize the template for your specific needs
5. The template loads directly into the Fixer tab for immediate use

#### Visualizing Workflows

1. Paste or load a workflow YAML file
2. Switch to the **Workflow Preview** tab
3. Click **Generate Diagram**
4. View the visual representation of jobs and their dependencies
5. Use this to document or explain workflows to team members

---

## 🛠️ Supported Workflow Types

Workflow YAML Fixer Pro supports the full range of GitHub Actions workflows:

### Continuous Integration

- **Node.js/TypeScript**: npm, yarn, pnpm workflows
- **Python**: pip, poetry, conda environments
- **Java**: Maven, Gradle build systems
- **Go**: go mod, GOPATH workflows
- **Ruby**: bundle, rake workflows
- **.NET/C#**: dotnet CLI workflows

### Container & Docker

- **Docker Build**: Multi-stage Docker builds
- **Container Registry**: Push to GHCR, Docker Hub, ECR
- **docker-compose**: Integration testing with databases
- **Kubernetes**: Deployment to K8s clusters

### Security & Compliance

- **Dependency Scanning**: Dependabot, dependency-review-action
- **SAST/DAST**: CodeQL, Semgrep, OWASP ZAP
- **Container Scanning**: Trivy, Clair, Snyk
- **Secret Scanning**: TruffleHog, git-secrets

### Cloud Deployments

- **AWS**: S3, Lambda, ECS, EKS, Beanstalk
- **Azure**: App Service, Functions, AKS
- **GCP**: Cloud Run, GKE, Cloud Functions
- **Firebase**: Hosting, Functions
- **Vercel**: Zero-config deployments
- **Netlify**: Static site deployments

### Automation & Bots

- **Issue Management**: Auto-label, triaging, closing
- **PR Management**: Auto-merge, review assignments
- **Notifications**: Slack, Discord, Teams integrations
- **Scheduled Tasks**: Cron-based automation

---

## 🔧 Technical Details

### Architecture

Workflow YAML Fixer Pro is built as a **single-file static web application** with no build step required:

- **HTML5**: Semantic markup for accessibility
- **CSS3**: Modern styling with CSS Grid, Flexbox, and CSS Variables
- **Vanilla JavaScript**: No frameworks or external dependencies
- **Client-side Processing**: All YAML parsing happens in the browser

### Browser Compatibility

The tool is compatible with all modern browsers:

- Chrome 80+
- Firefox 75+
- Safari 13+
- Edge 80+
- Opera 67+

### Performance

- **Zero Server Requests**: All processing happens locally
- **Instant Load**: Single HTML file with no external dependencies
- **Memory Efficient**: Streams large YAML files without full buffering
- **Offline Capable**: Works without internet connection after initial load

### Security

- **Client-side Only**: Your YAML data never leaves your browser
- **No Tracking**: No analytics or user monitoring
- **No External Scripts**: All code is bundled in the single HTML file
- **Local Storage**: Optional history saved only to your browser

---

## 🤝 Contributing

We welcome contributions from the community! Whether you want to add features, fix bugs, or improve documentation, your help is appreciated.

### Ways to Contribute

1. **Report Bugs**: Found an issue? Open a GitHub issue with details
2. **Feature Requests**: Have an idea? Share it in the discussions
3. **Documentation**: Improve README, add examples, or create wiki content
4. **Code Contributions**: Submit PRs for fixes or new features
5. **Templates**: Contribute new workflow templates for common use cases

### Development Setup

```bash
# Fork the repository on GitHub

# Clone your fork
git clone https://github.com/YOUR_USERNAME/workflow-yaml-fixer-pro.git

# Create a feature branch
git checkout -b feature/amazing-new-feature

# Make your changes
# Edit index.html with your improvements

# Test locally
open index.html

# Commit and push
git add .
git commit -m "Add amazing new feature"
git push origin feature/amazing-new-feature

# Open a Pull Request
```

### Coding Standards

- Use ES5-compatible JavaScript for maximum browser support
- Follow the existing code style and formatting
- Add comments for complex logic
- Test your changes in multiple browsers
- Update documentation as needed

---

## 📄 License

This project is licensed under the MIT License—a permissive open-source license that allows you to use, modify, and distribute the code freely.

**MIT License Summary:**

- ✅ Commercial use allowed
- ✅ Modification allowed
- ✅ Distribution allowed
- ✅ Private use allowed
- ✅ No warranty provided
- ✅ Attribution required

See the [LICENSE](LICENSE) file for the full license text.

---

## 🙏 Acknowledgments

This project was inspired by and built for the GitHub Actions community:

- **[GitHub Actions](https://github.com/features/actions)**: The powerful CI/CD platform that makes this tool necessary
- **[YAML Specification](https://yaml.org/)**: The human-friendly data serialization format we all love (and sometimes struggle with)
- **[GitHub Community](https://github.com/community)**: The maintainers and users who share knowledge daily
- **All Contributors**: Everyone who submits issues, PRs, or feedback

Special thanks to the maintainers of GitHub Actions for continuously improving the platform and providing clearer error messages that make tools like this possible.

---

## 🔗 Resources

### Quick Links

| Resource | URL |
|----------|-----|
| **Live Demo** | [https://CreoDAMO.github.io/workflow-yaml-fixer-pro/](https://CreoDAMO.github.io/workflow-yaml-fixer-pro/) |
| **Repository** | [https://github.com/CreoDAMO/workflow-yaml-fixer-pro](https://github.com/CreoDAMO/workflow-yaml-fixer-pro) |
| **Issues** | [https://github.com/CreoDAMO/workflow-yaml-fixer-pro/issues](https://github.com/CreoDAMO/workflow-yaml-fixer-pro/issues) |
| **License** | [https://github.com/CreoDAMO/workflow-yaml-fixer-pro/blob/main/LICENSE](https://github.com/CreoDAMO/workflow-yaml-fixer-pro/blob/main/LICENSE) |

### Related Resources

| Resource | Description |
|----------|-------------|
| [GitHub Actions Documentation](https://docs.github.com/en/actions) | Official GitHub Actions documentation |
| [YAML 1.2 Specification](https://yaml.org/spec/1.2.2/) | Complete YAML specification |
| [GitHub Actions Syntax](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions) | Workflow YAML syntax reference |
| [Actions Marketplace](https://github.com/marketplace?category=actions) | Browse thousands of community actions |

---

<div align="center">

**Made with ❤️ by the community, for the community**

[⭐ Star us on GitHub](https://github.com/CreoDAMO/workflow-yaml-fixer-pro/stargazers) • [🐛 Report an Issue](https://github.com/CreoDAMO/workflow-yaml-fixer-pro/issues) • [📖 Read the Docs](https://docs.github.com/en/actions)

</div>
