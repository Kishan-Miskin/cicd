<div align="center">

<br/>

```
 ██████╗██╗      ██████╗██████╗ 
██╔════╝██║     ██╔════╝██╔══██╗
██║     ██║     ██║     ██║  ██║
██║     ██║     ██║     ██║  ██║
╚██████╗██║     ╚██████╗██████╔╝
 ╚═════╝╚═╝      ╚═════╝╚═════╝ 
```

# ⚡ CI/CD Pipeline

**Automate. Integrate. Deploy. Repeat.**

[![CI Status](https://img.shields.io/github/actions/workflow/status/Kishan-Miskin/cicd/main.yml?style=for-the-badge&logo=githubactions&logoColor=white&label=CI%2FCD&color=22c55e)](https://github.com/Kishan-Miskin/cicd/actions)
[![Last Commit](https://img.shields.io/github/last-commit/Kishan-Miskin/cicd?style=for-the-badge&logo=git&logoColor=white&color=3b82f6)](https://github.com/Kishan-Miskin/cicd/commits/main)
[![License](https://img.shields.io/github/license/Kishan-Miskin/cicd?style=for-the-badge&color=a855f7)](./LICENSE)
[![Issues](https://img.shields.io/github/issues/Kishan-Miskin/cicd?style=for-the-badge&color=f59e0b)](https://github.com/Kishan-Miskin/cicd/issues)

<br/>

> *A battle-tested CI/CD pipeline that takes your code from commit to production —*  
> *automatically, reliably, and at speed.*

<br/>

</div>

---

<br/>

## 📌 Table of Contents

- [Overview](#-overview)
- [Pipeline Architecture](#-pipeline-architecture)
- [Tech Stack](#-tech-stack)
- [Getting Started](#-getting-started)
- [Pipeline Stages](#-pipeline-stages)
- [Configuration](#-configuration)
- [Environment Variables](#-environment-variables)
- [Contributing](#-contributing)
- [License](#-license)

<br/>

---

<br/>

## 🔭 Overview

This repository implements a **production-grade CI/CD pipeline** designed to eliminate manual deployment friction and enforce code quality at every stage. Whether you're pushing a hotfix or shipping a major feature, the pipeline ensures every change is built, tested, and deployed with confidence.

```
Developer pushes code
        │
        ▼
┌───────────────┐     ┌───────────────┐     ┌───────────────┐     ┌───────────────┐
│   🔍 Lint &   │────▶│  🧪 Run Tests │────▶│  🐳 Build &   │────▶│  🚀 Deploy to │
│   Static      │     │  & Coverage   │     │  Push Image   │     │  Production   │
│   Analysis    │     │               │     │               │     │               │
└───────────────┘     └───────────────┘     └───────────────┘     └───────────────┘
```

**Key goals:**
- ✅ Zero-downtime deployments
- ✅ Fail fast, fail loudly — broken builds never reach production
- ✅ Full audit trail for every deployment
- ✅ Easy rollback to any previous state

<br/>

---

<br/>

## 🏗️ Pipeline Architecture

```
 ┌─────────────────────────────────────────────────────────────────┐
 │                        GitHub Repository                        │
 │                                                                 │
 │   main branch ──────────────────────────────▶ Production        │
 │   dev branch  ────────────────▶ Staging                         │
 │   feature/*   ──▶ PR Checks                                     │
 └─────────────────────────────────────────────────────────────────┘
          │                │                 │
          ▼                ▼                 ▼
    ┌──────────┐     ┌──────────┐     ┌──────────┐
    │  GitHub  │     │  Docker  │     │  Cloud   │
    │  Actions │     │   Hub /  │     │  Provider│
    │          │     │   ECR    │     │ (AWS/GCP)│
    └──────────┘     └──────────┘     └──────────┘
```

<br/>

---

<br/>

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| **CI/CD Orchestration** | GitHub Actions |
| **Containerization** | Docker |
| **Container Registry** | Docker Hub / AWS ECR |
| **Infrastructure** | AWS / GCP / Kubernetes |
| **Code Quality** | ESLint / Pylint / SonarQube |
| **Testing** | Jest / PyTest / JUnit |
| **Secrets Management** | GitHub Secrets / Vault |
| **Notifications** | Slack / Email |

<br/>

---

<br/>

## 🚀 Getting Started

### Prerequisites

Make sure you have the following installed:

```bash
# Check Docker
docker --version       # >= 20.10

# Check Git
git --version          # >= 2.30
```

### Clone the Repository

```bash
git clone https://github.com/Kishan-Miskin/cicd.git
cd cicd
```

### Configure Secrets

Go to your GitHub repository → **Settings** → **Secrets and variables** → **Actions**, and add the following secrets:

```
DOCKER_USERNAME       → Your Docker Hub username
DOCKER_PASSWORD       → Your Docker Hub access token
DEPLOY_HOST           → Your server IP / hostname
DEPLOY_SSH_KEY        → Private SSH key for deployment server
```

### Trigger the Pipeline

```bash
# Push to any branch to trigger CI checks
git push origin feature/my-feature

# Merge to main to trigger full CI/CD to production
git checkout main
git merge feature/my-feature
git push origin main
```

<br/>

---

<br/>

## 🔁 Pipeline Stages

### 1. 🔍 Code Quality

```yaml
- Linting (ESLint / Pylint)
- Static analysis
- Dependency vulnerability scan
```

> Runs on every push and pull request. PRs cannot be merged if this stage fails.

---

### 2. 🧪 Test Suite

```yaml
- Unit tests
- Integration tests
- Code coverage report (must be ≥ 80%)
```

> Coverage reports are posted as PR comments automatically.

---

### 3. 🐳 Docker Build

```yaml
- Build Docker image
- Tag with Git SHA and branch name
- Push to container registry
```

```bash
# Images are tagged as:
ghcr.io/kishan-miskin/cicd:<git-sha>
ghcr.io/kishan-miskin/cicd:latest       # only on main
```

---

### 4. 🚀 Deploy

```yaml
- Pull latest image on the server
- Run zero-downtime container swap
- Health check — rollback on failure
- Notify team on Slack
```

<br/>

---

<br/>

## ⚙️ Configuration

The workflow configuration lives in `.github/workflows/`:

```
.github/
└── workflows/
    ├── ci.yml          # Runs on every push — lint + test
    ├── cd.yml          # Runs on main — build + deploy
    └── pr-check.yml    # Runs on pull requests
```

**Example — `ci.yml` snippet:**

```yaml
name: CI

on:
  push:
    branches: ["*"]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Install dependencies
        run: npm install
      - name: Run tests
        run: npm test -- --coverage
```

<br/>

---

<br/>

## 🔐 Environment Variables

| Variable | Description | Required |
|---|---|:---:|
| `DOCKER_USERNAME` | Container registry username | ✅ |
| `DOCKER_PASSWORD` | Container registry token | ✅ |
| `DEPLOY_HOST` | Target server hostname or IP | ✅ |
| `DEPLOY_SSH_KEY` | SSH private key for deployment | ✅ |
| `SLACK_WEBHOOK_URL` | Slack notifications webhook | ⬜ |
| `SONAR_TOKEN` | SonarQube analysis token | ⬜ |

<br/>

---

<br/>

## 🤝 Contributing

Contributions are welcome! Here's the flow:

```
1. Fork the repo
2. Create your branch:  git checkout -b feat/amazing-improvement
3. Commit changes:      git commit -m "feat: add amazing improvement"
4. Push the branch:     git push origin feat/amazing-improvement
5. Open a Pull Request
```

Please follow [Conventional Commits](https://www.conventionalcommits.org/) for commit messages.

<br/>

---

<br/>

## 📄 License

Distributed under the MIT License. See [`LICENSE`](./LICENSE) for more information.

<br/>

---

<br/>

<div align="center">

Made with ⚡ by [Kishan Miskin](https://github.com/Kishan-Miskin)

*If this repo helped you, consider giving it a ⭐*

</div>
