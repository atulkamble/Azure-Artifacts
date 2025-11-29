# 🚀 **Azure Artifacts – Complete Project (Code + Steps)**

## 📌 **1. Create Azure Artifacts Feed**

Go to:

**Azure DevOps → Artifacts → Create Feed**

```
Feed Name: cloudnautic-feed
Visibility: Private
Upstream Sources: Enable (optional)
```

---

# 🔼 **2. Configure Upstream Sources**

Azure Artifacts supports upstream sources:

### **Enable from Portal**

Artifacts → Feed → *cloudnautic-feed* → **Upstream sources** → Add

### Options

| Source Type   | Example         |
| ------------- | --------------- |
| NuGet.org     | `.NET packages` |
| npmjs.com     | `npm packages`  |
| PyPI          | Python packages |
| Maven Central | Java packages   |

### Example: Add npm upstream

```bash
npm config set registry https://pkgs.dev.azure.com/<ORG_NAME>/_packaging/cloudnautic-feed/npm/registry/
```

---

# 📦 **3. Publish Package to Azure Artifacts Feed**

Below are examples for **npm**, **NuGet**, and **Python**.

---

# 🟦 A) **Publish npm package**

### **package.json**

```json
{
  "name": "@cloudnautic/hello-world",
  "version": "1.0.0",
  "main": "index.js",
  "author": "Atul Kamble",
  "license": "MIT"
}
```

### **index.js**

```js
module.exports = () => {
  console.log("Hello from Cloudnautic Azure Artifacts!");
};
```

### **npmrc**

```
registry=https://pkgs.dev.azure.com/<ORG>/<PROJECT>/_packaging/cloudnautic-feed/npm/registry/
always-auth=true
```

---

### **Azure Pipeline – Publish npm Package**

`azure-pipelines.yml`

```yaml
trigger:
- main

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: NodeTool@0
  inputs:
    versionSpec: '18.x'

- script: |
    echo "Authenticating npm to Azure Artifacts..."
    npm config set registry $(registryURL)
    npm config set always-auth true
    npm config set //pkgs.dev.azure.com/<ORG>/<PROJECT>/_packaging/cloudnautic-feed/npm/registry/:_authToken=$(System.AccessToken)

  displayName: Authenticate npm

- script: |
    npm install
    npm version patch
    npm publish
  displayName: Publish npm package
```

Variables:

```
registryURL: https://pkgs.dev.azure.com/<ORG>/<PROJECT>/_packaging/cloudnautic-feed/npm/registry/
```

---

# 🟪 B) **Publish NuGet (.NET) Package**

### **.csproj**

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <PackageId>Cloudnautic.HelloWorld</PackageId>
    <Version>1.0.0</Version>
    <Authors>Atul Kamble</Authors>
  </PropertyGroup>
</Project>
```

---

### **Azure Pipeline YAML – Publish NuGet Package**

```yaml
trigger:
- main

pool:
  vmImage: 'windows-latest'

steps:
- task: DotNetCoreCLI@2
  displayName: Restore
  inputs:
    command: 'restore'
    feedsToUse: 'select'
    vstsFeed: 'cloudnautic-feed'

- task: DotNetCoreCLI@2
  displayName: Pack
  inputs:
    command: 'pack'
    packagesToPack: '**/*.csproj'
    versioningScheme: 'off'

- task: DotNetCoreCLI@2
  displayName: Push
  inputs:
    command: 'push'
    packagesToPush: '$(Build.ArtifactStagingDirectory)/*.nupkg'
    feedPublish: 'cloudnautic-feed'
```

---

# 🟩 C) **Publish Python Package (Wheel)**

### **setup.py**

```python
from setuptools import setup

setup(
    name="cloudnautic-hello",
    version="1.0.0",
    description="Hello package from cloudnautic",
    author="Atul Kamble",
    packages=["cloudnautic"],
)
```

### **folder: cloudnautic/**init**.py**

```python
print("Hello from Cloudnautic Python Package")
```

---

### **Azure Pipeline – Publish Python Wheel**

```yaml
trigger: [main]

pool:
  vmImage: ubuntu-latest

steps:
- script: pip install twine setuptools wheel keyring artifacts-keyring
  displayName: Install dependencies

- script: |
    python setup.py sdist bdist_wheel
  displayName: Build wheel

- script: |
    python -m twine upload --repository-url https://pkgs.dev.azure.com/<ORG>/<PROJECT>/_packaging/cloudnautic-feed/pypi/upload/ -u az -p $(System.AccessToken) dist/*
  displayName: Upload package
```

---

# 🔽 **4. Consume Packages from Azure Artifacts Feed**

## npm:

```bash
npm install @cloudnautic/hello-world
```

Add `.npmrc`:

```
@cloudnautic:registry=https://pkgs.dev.azure.com/<ORG>/<PROJECT>/_packaging/cloudnautic-feed/npm/registry/
always-auth=true
```

---

## NuGet:

```xml
<package id="Cloudnautic.HelloWorld" version="1.0.0" />
```

DevOps automatically authenticates using pipeline identity.

---

## Python:

Add:

```
index-url=https://pkgs.dev.azure.com/<ORG>/<PROJECT>/_packaging/cloudnautic-feed/pypi/simple/
```

Install:

```bash
pip install cloudnautic-hello
```

---

# ❌🗑️ **5. Delete & Recover Package (Recycle Bin)**

## Delete Package

Azure DevOps → Artifacts → Feed → Package → **Delete**

## CLI delete (nuget)

```bash
nuget delete Cloudnautic.HelloWorld 1.0.0 -Source cloudnautic-feed -ApiKey AzureDevOps
```

---

## Recover Package

Artifacts → Feed → **Recycle Bin** → Restore

---

# 🔐 **6. Private vs Public Feeds**

| Feature          | Private Feed                     | Public (Organization)       |
| ---------------- | -------------------------------- | --------------------------- |
| Access           | Only authorized users            | Anyone in organization      |
| Authentication   | Required                         | DevOps login                |
| CI/CD pipelines  | Require System.AccessToken       | No extra steps              |
| Internet access  | Cannot access without permission | Requires Azure DevOps login |
| Upstream sources | Supports caching                 | Yes                         |

🔒 **Private feed recommended for secure enterprise code**
🌍 **Public feed useful for internal teams within same organization**

