**complete, production-ready `README.md`** for **Azure Artifacts** with
✅ Architecture diagrams
✅ Workflow diagrams
✅ Commands
✅ Basic examples for npm, NuGet, Python, Maven, Universal Packages
---

# 📘 **Azure Artifacts – Complete Documentation (README.md)**

````markdown
# 🎯 Azure Artifacts – Complete Guide

Azure Artifacts is a package management service inside Azure DevOps that allows teams to  
store, publish, version, and share packages such as:

- **npm**
- **NuGet**
- **Python**
- **Maven**
- **Cargo**
- **Universal Packages**

This guide explains setup, workflows, authentication, and basic examples.

---

# 📦 What Is Azure Artifacts?

Azure Artifacts provides:

- ✔ Private & secure package feeds  
- ✔ Versioned package distribution  
- ✔ CI/CD integration (Azure Pipelines, GitHub Actions, Jenkins)  
- ✔ Upstream sources (npmjs, NuGet.org, PyPI, Maven Central)  
- ✔ Retention policies and storage monitoring  

---

# 🏗 Azure Artifacts Architecture Diagram

```mermaid
flowchart LR
    Devs[Developers] --> Publish
    CI[CI/CD Pipeline] --> Publish
    Publish --> Feed[Azure Artifacts Feed]

    Feed -->|npm install| App1
    Feed -->|dotnet restore| App2
    Feed -->|pip install| App3
    Feed -->|mvn install| App4

    subgraph External Sources
        npmjs[npmjs.com]
        nuget[NuGet.org]
        pypi[PyPI]
        maven[Maven Central]
    end

    External Sources --> Upstream[Upstream Sources]
    Upstream --> Feed
````

---

# 🔁 End-to-End Azure Artifacts Workflow

```mermaid
sequenceDiagram
    participant Dev as Developer
    participant CLI as Package CLI (npm/pip/dotnet/mvn)
    participant Feed as Azure Artifacts Feed
    participant Pipeline as Azure DevOps Pipeline

    Dev->>CLI: Create Package
    CLI->>Dev: Build Package
    Dev->>Feed: Publish Package
    Pipeline->>Feed: Download Package
    Feed->>Pipeline: Provide version
    Pipeline->>Dev: Deployed application
```

---

# 🚀 Getting Started

### 1️⃣ **Create or use existing Azure DevOps Organization**

[https://dev.azure.com/](https://dev.azure.com/)

### 2️⃣ **Create a Feed**

> Azure DevOps → **Artifacts → Create Feed**

Choose:

* Public/Private
* Enable upstreams (optional)

### 3️⃣ **Connect to the Feed**

Azure DevOps shows options for:

* npm
* NuGet
* Python
* Maven
* Universal Packages
* Cargo

---

# 🔐 Authentication

Use **Azure Artifacts Credential Provider** or **Personal Access Token (PAT)**
with **Packaging Read/Write** scopes.

PAT example usage:

```bash
USERNAME="Azure DevOps"
PASSWORD="<YOUR_PAT>"
```

---

# 🟦 npm Example

## 1. **Configure npm**

Create `.npmrc`:

```
registry=https://pkgs.dev.azure.com/<ORG>/<PROJECT>/_packaging/<FEED>/npm/registry/
always-auth=true
```

Login:

```bash
npm login --registry=https://pkgs.dev.azure.com/<ORG>/<PROJECT>/_packaging/<FEED>/npm/registry/
```

## 2. **Create package**

```bash
npm init -y
```

`index.js`

```javascript
module.exports = () => console.log("Hello from Azure Artifacts npm package!");
```

## 3. **Publish**

```bash
npm publish
```

---

# 🟪 Python Example (pip + twine)

## 1. **Create simple package**

`hello.py`

```python
def greet():
    return "Hello from Azure Artifacts Python package!"
```

`setup.py`

```python
from setuptools import setup

setup(
    name="mypyapp",
    version="0.0.1",
    py_modules=["hello"]
)
```

## 2. **Build**

```bash
python3 setup.py sdist bdist_wheel
```

## 3. **Publish**

```bash
twine upload --repository-url \
https://pkgs.dev.azure.com/<ORG>/<PROJECT>/_packaging/<FEED>/pypi/upload \
dist/*
```

## 4. **Install**

```bash
pip install mypyapp --index-url \
https://pkgs.dev.azure.com/<ORG>/<PROJECT>/_packaging/<FEED>/pypi/simple/
```

---

# 🟧 Maven Example

## settings.xml

```xml
<servers>
  <server>
    <id>azure-artifacts</id>
    <username>Azure DevOps</username>
    <password>YOUR_PAT</password>
  </server>
</servers>
```

## pom.xml

```xml
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.demo</groupId>
  <artifactId>hello-artifacts</artifactId>
  <version>1.0.0</version>
  <distributionManagement>
    <repository>
      <id>azure-artifacts</id>
      <url>https://pkgs.dev.azure.com/ORG/PROJECT/_packaging/FEED/maven/v1</url>
    </repository>
  </distributionManagement>
</project>
```

## Publish

```bash
mvn deploy
```

---

# 🟩 NuGet (.NET) Example

## 1. Pack

```bash
dotnet pack -o out
```

## 2. Publish

```bash
dotnet nuget push out/*.nupkg \
  --source "https://pkgs.dev.azure.com/ORG/PROJECT/_packaging/FEED/nuget/v3/index.json" \
  --api-key <PAT>
```

## 3. Restore

```bash
dotnet restore
```

---

# 🟫 Universal Packages Example

## Publish

```bash
az artifacts universal publish \
  --organization https://dev.azure.com/<ORG> \
  --feed <FEED> \
  --name demo \
  --version 1.0.0 \
  --path .
```

## Download

```bash
az artifacts universal download \
  --organization https://dev.azure.com/<ORG> \
  --feed <FEED> \
  --name demo \
  --version 1.0.0 \
  --path .
```

---

# 🟥 Cargo (Rust) Example

`.cargo/config`

```toml
[registries.azure]
index = "https://pkgs.dev.azure.com/<ORG>/<PROJECT>/_packaging/<FEED>/cargo/index"
```

## Publish

```bash
cargo publish --registry azure
```

## Install

```bash
cargo install <PKG> --registry azure
```

---

# 🧹 Feed Cleanup & Retention Rules

You can configure:

* Maximum versions
* Delete older artifacts automatically
* Storage usage monitoring

Azure DevOps → Artifacts → **Feed Settings → Policies**

---

# 🛠 Azure Pipelines Integration (Basic)

`azure-pipelines.yml`

```yaml
trigger:
- main

pool:
  vmImage: ubuntu-latest

steps:
- task: NuGetCommand@2
  inputs:
    command: 'restore'
    feedsToUse: 'select'
    vstsFeed: '<FEED>'
```

---

# 📊 Monitoring Storage

Go to:
**Artifacts → Storage → View usage**

Shows:

* Feed size
* Artifact count
* Storage trends

---

# 🧭 Best Practices

✔ Use separate feeds for Dev, QA, Prod
✔ Enable upstream sources for caching
✔ Enforce retention policies
✔ Use PAT only for CI/CD (not developers)
✔ Use versioning strategy: `MAJOR.MINOR.PATCH`

---

# 🎉 Conclusion

Azure Artifacts is a powerful, enterprise-grade package storage solution fully integrated with Azure DevOps, CI/CD, and upstream repositories.
This README provides all basic workflows, commands, diagrams, and publishing steps.

---


```
```
