# Azure Artifacts

## 1. Training Objectives

After completing this module, students will be able to:

* Understand Azure Artifacts and package feeds
* Create project-scoped and organization-scoped feeds
* Configure private and public package access
* Understand upstream sources
* Publish packages manually
* Publish packages using Azure Pipelines
* Consume packages from Azure Artifacts feeds
* Manage package versions
* Delete and recover packages
* Configure feed permissions
* Use feed views such as `@Local`, `@Prerelease`, and `@Release`

---

# 2. What Is Azure Artifacts?

Azure Artifacts is a package-management service inside Azure DevOps.

It allows development teams to:

* Store packages
* Publish packages
* Share packages
* Download packages
* Control package access
* Maintain package versions
* Consume packages from public repositories
* Use packages inside Azure Pipelines

Azure Artifacts supports:

* npm
* NuGet
* Maven
* Python
* Cargo
* Universal Packages

A single feed can store multiple supported package types.

---

# 3. Important Azure Artifacts Terminology

## 3.1 Package

A package is a reusable unit of software.

Examples:

* Python library
* npm library
* Java JAR file
* NuGet package
* Configuration files
* Build output
* Shell scripts
* Terraform modules
* Website files

Example package:

```text
cloudnautic-tools
Version: 1.0.0
```

---

## 3.2 Feed

A feed is a container used to store and manage packages.

Example:

```text
Feed Name: cloudnautic-feed
```

Inside the feed:

```text
cloudnautic-tools       1.0.0
python-calculator       1.1.0
webapp-config           2.0.0
deployment-scripts      3.0.0
```

Azure Artifacts feeds help control package storage, access and sharing.

---

## 3.3 Package Version

Each package must have a version.

Examples:

```text
1.0.0
1.0.1
1.1.0
2.0.0
```

Azure Artifacts packages are immutable. After a package version is published, its files cannot be replaced, and the same version number cannot be reused—even after deletion.

Therefore, this will fail when `1.0.0` already exists:

```text
Package: cloudnautic-tools
Version: 1.0.0
```

Publish a new version instead:

```text
Package: cloudnautic-tools
Version: 1.0.1
```

---

## 3.4 Feed Views

Feed views allow teams to classify package versions according to their quality or release stage.

Default views include:

```text
@Local
@Prerelease
@Release
```

Packages are published to the base feed and initially appear in `@Local`. They can then be promoted to another view.

Example flow:

```text
Package published
       |
       v
    @Local
       |
       v
 @Prerelease
       |
       v
   @Release
```

### Meaning

| View          | Purpose                            |
| ------------- | ---------------------------------- |
| `@Local`      | Newly published or cached packages |
| `@Prerelease` | Testing or staging packages        |
| `@Release`    | Approved production packages       |

Packages can be promoted, but Azure Artifacts does not support demoting a package back to an earlier view.

---

# 4. Azure Artifacts Architecture

```text
Developer
    |
    | Publish package
    v
Azure Artifacts Feed
    |
    | Store package and version
    v
Azure DevOps Project
    |
    | Consume package
    v
Application / Pipeline / Build
```

With upstream sources:

```text
Developer
    |
    | Install package
    v
Azure Artifacts Feed
    |
    | Package available?
    |
    +---- Yes ----> Download from feed
    |
    +---- No -----> Public Registry
                       |
                       v
              npmjs / PyPI / NuGet
                       |
                       v
             Save package in feed
```

---

# 5. Feed Scope

Azure Artifacts supports two common feed scopes.

## 5.1 Project-Scoped Feed

A project-scoped feed belongs to a particular Azure DevOps project.

Example:

```text
Organization: cloudnautic
Project: artifacts-project
Feed: cloudnautic-feed
```

Feed URL structure:

```text
https://pkgs.dev.azure.com/cloudnautic/artifacts-project/_packaging/cloudnautic-feed/
```

Use a project-scoped feed when:

* Packages belong to one project
* Access must be controlled at project level
* The project has an independent development team

---

## 5.2 Organization-Scoped Feed

An organization-scoped feed can be used by multiple projects in the same Azure DevOps organization.

Example:

```text
Organization: cloudnautic
Feed: common-packages
```

Feed URL structure:

```text
https://pkgs.dev.azure.com/cloudnautic/_packaging/common-packages/
```

Use it when:

* Multiple projects share common libraries
* Centralized package management is required
* A platform team maintains reusable packages

---

# 6. Create an Azure Artifacts Feed

## Azure DevOps Details

```text
Organization:
https://dev.azure.com/cloudnautic

Project:
artifacts-project

Feed:
cloudnautic-feed
```

## Portal Steps

1. Sign in to Azure DevOps.
2. Open your organization.
3. Open the required project.
4. Select **Artifacts**.
5. Select **Create Feed**.
6. Enter the feed name:

```text
cloudnautic-feed
```

7. Select feed visibility.
8. Select project or organization scope.
9. Enable upstream sources when required.
10. Select **Create**.

Azure DevOps lets you define the feed name, visibility, scope and upstream-source setting during feed creation.

---

# 7. Private vs Public Feeds

## 7.1 Private Feed

A private feed is accessible only to authorized users, groups and pipelines.

Use a private feed for:

* Internal company libraries
* Proprietary source code
* Private Python packages
* Internal npm packages
* Deployment scripts
* Configuration files
* Organization-specific tools

Example:

```text
cloudnautic-internal-packages
```

Advantages:

* Better security
* Permission-based access
* Controlled publishing
* Suitable for internal applications
* Integration with Azure DevOps identities

---

## 7.2 Public Feed

A public feed is intended for packages that can be consumed by broader audiences without the same private-project restrictions.

Use a public feed for:

* Open-source packages
* Public SDKs
* Sample libraries
* Public training packages
* Community tools

Do not publish the following in a public feed:

```text
Passwords
API keys
Connection strings
Private certificates
Company source code
Environment files
Customer data
```

---

## 7.3 Comparison

| Feature               | Private Feed           | Public Feed                         |
| --------------------- | ---------------------- | ----------------------------------- |
| Access                | Authorized users       | Broader or anonymous audience       |
| Authentication        | Normally required      | May not be required for consumption |
| Internal libraries    | Recommended            | Not recommended                     |
| Open-source packages  | Possible               | Recommended                         |
| Sensitive code        | Suitable with controls | Never publish                       |
| Permission management | Strongly controlled    | Public visibility                   |
| Typical use           | Enterprise development | Community distribution              |

---

# 8. Feed Permissions

Important Azure Artifacts roles include:

## 8.1 Feed Reader

Can:

* View packages
* Download packages
* Consume packages

Cannot:

* Publish packages
* Delete packages
* Change settings

---

## 8.2 Feed and Upstream Reader — Collaborator

Can:

* Download packages
* Use packages from upstream sources
* Save upstream packages into the feed

A Collaborator role or higher is required to save packages from upstream sources.

---

## 8.3 Feed Publisher — Contributor

Can:

* Publish packages
* Download packages
* Promote packages
* Manage package metadata

---

## 8.4 Feed Owner

Can:

* Manage feed settings
* Manage permissions
* Configure retention
* Delete packages
* Restore packages
* Delete the feed

Feed Owners are required for package recovery and retention-policy management.

---

# 9. Give Azure Pipeline Permission to a Feed

A pipeline uses an Azure DevOps build-service identity.

Typical identities:

```text
Project Name Build Service (Organization Name)
```

Example:

```text
artifacts-project Build Service (cloudnautic)
```

Another identity can be:

```text
Project Collection Build Service (cloudnautic)
```

## Steps

1. Open **Artifacts**.
2. Open `cloudnautic-feed`.
3. Select **Feed Settings**.
4. Select **Permissions**.
5. Select **Add users/groups**.
6. Add:

```text
artifacts-project Build Service (cloudnautic)
```

7. Assign:

```text
Feed Publisher (Contributor)
```

For pipelines that publish packages, Microsoft recommends granting the relevant build-service identities the Feed Publisher role.

---

# 10. Upstream Sources

An upstream source allows an Azure Artifacts feed to obtain packages from another package source.

Examples:

* npmjs.com
* PyPI
* NuGet.org
* Maven Central
* Another Azure Artifacts feed

Azure Artifacts can save packages obtained from public registries into your own feed.

---

## 10.1 Why Use Upstream Sources?

Without upstream sources:

```text
Application ---> Public Registry
```

With upstream sources:

```text
Application ---> Azure Artifacts Feed ---> Public Registry
```

Benefits:

* Single package endpoint
* Dependency caching
* Better package availability
* Centralized access control
* Reduced dependency on public registries
* Consistent package versions
* Protection against temporary public-registry outages

---

## 10.2 Example

The application requests:

```text
express@5.1.0
```

Azure Artifacts checks:

```text
Is express@5.1.0 available in cloudnautic-feed?
```

If no:

```text
Azure Artifacts -> npmjs.com
```

The package is downloaded and saved in the feed.

For future requests:

```text
Developer -> cloudnautic-feed -> saved express package
```

---

## 10.3 Enable Upstream Sources

### During Feed Creation

Enable:

```text
Include packages from common public sources
```

### Existing Feed

1. Open **Artifacts**.
2. Select the feed.
3. Open **Feed Settings**.
4. Select **Upstream Sources**.
5. Select **Add Upstream**.
6. Select a package source.
7. Save the settings.

---

# 11. Training Lab 1 — Universal Package

Universal Packages can store almost any collection of files.

Examples:

* Scripts
* ZIP files
* Website content
* Infrastructure templates
* Configuration files
* Documentation
* Binary files

---

## 11.1 Repository Structure

```text
azure-artifacts-practice/
├── package/
│   ├── app.py
│   ├── config.json
│   └── deploy.sh
├── .artifactignore
└── azure-pipelines.yml
```

---

## 11.2 Create the Project

```bash
git clone https://github.com/atulkamble/azure-artifacts-practice.git
cd azure-artifacts-practice
```

---

## 11.3 Python Application

File:

```text
package/app.py
```

Code:

```python
def hello() -> None:
    print("Hello from the Azure Artifacts package")


if __name__ == "__main__":
    hello()
```

Run:

```bash
python package/app.py
```

Expected output:

```text
Hello from the Azure Artifacts package
```

---

## 11.4 Configuration File

File:

```text
package/config.json
```

Code:

```json
{
  "application": "cloudnautic-app",
  "environment": "training",
  "version": "1.0.0"
}
```

---

## 11.5 Deployment Script

File:

```text
package/deploy.sh
```

Code:

```bash
#!/bin/bash

echo "Starting application deployment"
echo "Package version: 1.0.0"
python app.py
echo "Deployment completed"
```

Give execute permission:

```bash
chmod +x package/deploy.sh
```

---

## 11.6 `.artifactignore`

File:

```text
.artifactignore
```

Code:

```gitignore
.git
.gitignore
azure-pipelines.yml
README.md
*.log
__pycache__/
.env
```

`.artifactignore` controls files excluded when publishing Universal Packages and pipeline artifacts.

---

# 12. Install Azure DevOps CLI Extension

Check Azure CLI:

```bash
az version
```

Install the Azure DevOps extension:

```bash
az extension add --name azure-devops
```

Update it when already installed:

```bash
az extension update --name azure-devops
```

Sign in:

```bash
az login
```

Sign in to Azure DevOps:
```
az devops login
```
if want to logout existing 

check details 
```
az version
az account show
env | grep AZURE_DEVOPS
```
```
az devops configure --list
```
and 
```
az config get extension.use_dynamic_install
```
```
az devops logout
unset AZURE_DEVOPS_EXT_PAT
unset AZURE_DEVOPS_EXT_ARTIFACTTOOL_PATVAR
rm -f ~/.azure/azuredevops/cli/config
```
## set new
```
export AZURE_DEVOPS_EXT_PAT="PASTE_NEW_PAT_HERE"
```

Create and Update Token form Azure DevOps Settings (PAT)
Add Token: 

Configure the default organization:

```bash
az devops configure --defaults organization=https://dev.azure.com/cloudnautic
```

Configure the default project:

```bash
az devops configure --defaults project=project

https://dev.azure.com/cloudnautic/project

az devops configure \
  --defaults project=artifacts-project
```

---

# 13. Publish Universal Package Using CLI

## 13.1 Variables

```bash
ORGANIZATION="https://dev.azure.com/cloudnautic"
PROJECT="project"
FEED="cloudnautic-feed"
PACKAGE_NAME="cloudnautic-tools"
PACKAGE_VERSION="1.0.4"
PACKAGE_PATH="./package"
```

## Verify 
```
echo "$ORGANIZATION"
echo "$PROJECT"
echo "$FEED"
echo "$PACKAGE_NAME"
echo "$PACKAGE_VERSION"
echo "$PACKAGE_PATH"
```

## show project 
```
az devops project show \
  --organization "https://dev.azure.com/cloudnautic" \
  --project "project"
```

## test feed access 
```
az artifacts feed show \
  --organization "https://dev.azure.com/cloudnautic" \
  --project "project" \
  --feed "cloudnautic-feed"
```
---

## verify the feed using the Azure DevOps REST API
```
az devops invoke \
  --organization "https://dev.azure.com/cloudnautic" \
  --area packaging \
  --resource feeds \
  --route-parameters project="project" \
  --api-version "7.1-preview" \
  --output json
```

## 13.2 Publish to Project-Scoped Feed

```bash

az artifacts universal publish \
  --organization "$ORGANIZATION" \
  --project "$PROJECT" \
  --scope project \
  --feed "$FEED" \
  --name "$PACKAGE_NAME" \
  --version "$PACKAGE_VERSION" \
  --description "Cloudnautic training package" \
  --path "$PACKAGE_PATH"
```

Universal Package names must follow Azure Artifacts naming rules and should be lowercase.

---

## 13.3 Publish to Organization-Scoped Feed

```bash
az artifacts universal publish \
  --organization "$ORGANIZATION" \
  --scope organization \
  --feed "$FEED" \
  --name "$PACKAGE_NAME" \
  --version "$PACKAGE_VERSION" \
  --description "Cloudnautic organization package" \
  --path "$PACKAGE_PATH"
```

---

## 13.4 Expected Result

```text
Package Name: cloudnautic-tools
Version: 1.0.0
Feed: cloudnautic-feed
```

Open:

```text
Azure DevOps
    -> Artifacts
    -> cloudnautic-feed
    -> cloudnautic-tools
```

---

# 14. Download Universal Package Using CLI

Create a download directory:

```bash
mkdir downloaded-package
```

Download:

```bash
az artifacts universal download \
  --organization "https://dev.azure.com/cloudnautic" \
  --project "artifacts-project" \
  --scope project \
  --feed "cloudnautic-feed" \
  --name "cloudnautic-tools" \
  --version "1.0.0" \
  --path "./downloaded-package"
```

Verify:

```bash
tree downloaded-package
```

Run the application:

```bash
python downloaded-package/app.py
```

---

# 15. Publish Package Using Azure Pipeline

File:

```text
azure-pipelines.yml
```

```yaml
trigger:
  - main

pool:
  vmImage: ubuntu-latest

steps:
  - script: |
      echo "Preparing Universal Package"
      ls -la
      find package -maxdepth 2 -type f
    displayName: Prepare Package

  - task: UniversalPackages@0
    displayName: Publish Universal Package
    inputs:
      command: publish
      publishDirectory: package
      feedsToUsePublish: internal
      vstsFeedPublish: artifacts-project/cloudnautic-feed
      vstsFeedPackagePublish: cloudnautic-tools
      versionOption: custom
      versionPublish: 1.0.$(Build.BuildId)
      packagePublishDescription: Cloudnautic training package
```

---

## 15.1 Version Example

Suppose:

```text
Build.BuildId = 25
```

Published package version:

```text
1.0.25
```

The next build might publish:

```text
1.0.26
```

This prevents duplicate-version errors.

---

# 16. Pipeline with Major, Minor and Patch Variables

```yaml
trigger:
  - main

pool:
  vmImage: ubuntu-latest

variables:
  major: 1
  minor: 0
  packageName: cloudnautic-tools
  feedName: artifacts-project/cloudnautic-feed

steps:
  - script: |
      echo "Package: $(packageName)"
      echo "Version: $(major).$(minor).$(Build.BuildId)"
    displayName: Display Package Details

  - task: UniversalPackages@0
    displayName: Publish Package
    inputs:
      command: publish
      publishDirectory: package
      feedsToUsePublish: internal
      vstsFeedPublish: $(feedName)
      vstsFeedPackagePublish: $(packageName)
      versionOption: custom
      versionPublish: $(major).$(minor).$(Build.BuildId)
      packagePublishDescription: Published using Azure Pipeline
```

---

# 17. Consume Universal Package in Azure Pipeline

```yaml
trigger:
  - main

pool:
  vmImage: ubuntu-latest

steps:
  - task: UniversalPackages@0
    displayName: Download Universal Package
    inputs:
      command: download
      downloadDirectory: $(Pipeline.Workspace)/cloudnautic-tools
      feedsToUse: internal
      vstsFeed: artifacts-project/cloudnautic-feed
      vstsFeedPackage: cloudnautic-tools
      vstsPackageVersion: 1.0.25

  - script: |
      echo "Downloaded package files:"
      find "$(Pipeline.Workspace)/cloudnautic-tools" -maxdepth 3 -type f

      python "$(Pipeline.Workspace)/cloudnautic-tools/app.py"
    displayName: Use Downloaded Package
```

---

# 18. Publish and Consume in Different Stages

```yaml
trigger:
  - main

pool:
  vmImage: ubuntu-latest

variables:
  packageVersion: 1.0.$(Build.BuildId)

stages:
  - stage: Publish
    displayName: Publish Package
    jobs:
      - job: PublishPackage
        steps:
          - task: UniversalPackages@0
            displayName: Publish Universal Package
            inputs:
              command: publish
              publishDirectory: package
              feedsToUsePublish: internal
              vstsFeedPublish: artifacts-project/cloudnautic-feed
              vstsFeedPackagePublish: cloudnautic-tools
              versionOption: custom
              versionPublish: $(packageVersion)
              packagePublishDescription: Package from build $(Build.BuildId)

  - stage: Consume
    displayName: Consume Package
    dependsOn: Publish
    jobs:
      - job: DownloadPackage
        steps:
          - task: UniversalPackages@0
            displayName: Download Universal Package
            inputs:
              command: download
              downloadDirectory: $(Pipeline.Workspace)/package
              feedsToUse: internal
              vstsFeed: artifacts-project/cloudnautic-feed
              vstsFeedPackage: cloudnautic-tools
              vstsPackageVersion: $(packageVersion)

          - script: |
              python "$(Pipeline.Workspace)/package/app.py"
            displayName: Run Package
```

---

# 19. Training Lab 2 — Create a Python Package

## 19.1 Project Structure

```text
cloudnautic-calculator/
├── cloudnautic_calculator/
│   ├── __init__.py
│   └── calculator.py
├── tests/
│   └── test_calculator.py
├── pyproject.toml
├── README.md
└── azure-pipelines.yml
```

---

## 19.2 Create Files

```bash
mkdir cloudnautic-calculator
cd cloudnautic-calculator

mkdir cloudnautic_calculator
mkdir tests

touch cloudnautic_calculator/__init__.py
touch cloudnautic_calculator/calculator.py
touch tests/test_calculator.py
touch pyproject.toml
touch README.md
touch azure-pipelines.yml
```

---

## 19.3 Calculator Code

File:

```text
cloudnautic_calculator/calculator.py
```

```python
def add(first_number: float, second_number: float) -> float:
    return first_number + second_number


def subtract(first_number: float, second_number: float) -> float:
    return first_number - second_number


def multiply(first_number: float, second_number: float) -> float:
    return first_number * second_number


def divide(first_number: float, second_number: float) -> float:
    if second_number == 0:
        raise ValueError("Division by zero is not allowed")

    return first_number / second_number
```

---

## 19.4 `__init__.py`

```python
from .calculator import add, divide, multiply, subtract

__all__ = ["add", "subtract", "multiply", "divide"]
```

---

## 19.5 Unit Test

File:

```text
tests/test_calculator.py
```

```python
from cloudnautic_calculator import add, divide, multiply, subtract


def test_add() -> None:
    assert add(10, 5) == 15


def test_subtract() -> None:
    assert subtract(10, 5) == 5


def test_multiply() -> None:
    assert multiply(10, 5) == 50


def test_divide() -> None:
    assert divide(10, 5) == 2
```

---

## 19.6 `pyproject.toml`

```toml
[build-system]
requires = ["setuptools>=77.0"]
build-backend = "setuptools.build_meta"

[project]
name = "cloudnautic-calculator"
version = "1.0.0"
description = "Basic calculator package for Azure Artifacts training"
readme = "README.md"
requires-python = ">=3.10"
authors = [
  { name = "Atul Kamble" }
]

[tool.pytest.ini_options]
pythonpath = ["."]
testpaths = ["tests"]
```

---

## 19.7 Build Python Package

Install tools:

```bash
python -m pip install --upgrade pip
python -m pip install build twine pytest
```

Run tests:

```bash
pytest
```

Build package:

```bash
python -m build
```

Output:

```text
dist/
├── cloudnautic_calculator-1.0.0-py3-none-any.whl
└── cloudnautic_calculator-1.0.0.tar.gz
```

---

# 20. Publish Python Package Manually

Open:

```text
Azure DevOps
    -> Artifacts
    -> cloudnautic-feed
    -> Connect to Feed
    -> Python
    -> twine
```

Create `.pypirc` using the feed-provided configuration.

Example structure:

```ini
[distutils]
index-servers =
    cloudnautic-feed

[cloudnautic-feed]
repository = https://pkgs.dev.azure.com/cloudnautic/artifacts-project/_packaging/cloudnautic-feed/pypi/upload/
username = cloudnautic
password = YOUR_PERSONAL_ACCESS_TOKEN
```

Do not commit `.pypirc` containing a token.

Add it to `.gitignore`:

```gitignore
.pypirc
.env
```

Publish:

```bash
python -m twine upload \
  --repository cloudnautic-feed \
  dist/*
```

Azure Artifacts supports publishing and downloading Python packages through feeds and consuming public Python packages through configured sources.

---

# 21. Publish Python Package Using Azure Pipeline

```yaml
trigger:
  - main

pool:
  vmImage: ubuntu-latest

steps:
  - task: UsePythonVersion@0
    displayName: Use Python
    inputs:
      versionSpec: "3.12"

  - script: |
      python -m pip install --upgrade pip
      python -m pip install build pytest
    displayName: Install Dependencies

  - script: |
      pytest
    displayName: Run Tests

  - script: |
      python -m build
      ls -la dist
    displayName: Build Python Package

  - task: TwineAuthenticate@1
    displayName: Authenticate with Azure Artifacts
    inputs:
      artifactFeed: artifacts-project/cloudnautic-feed

  - script: |
      python -m pip install twine

      python -m twine upload \
        --repository-url "$(PYPIRC_PATH)" \
        dist/*
    displayName: Publish Python Package
```

A more commonly used pipeline pattern is:

```yaml
trigger:
  - main

pool:
  vmImage: ubuntu-latest

steps:
  - task: UsePythonVersion@0
    inputs:
      versionSpec: "3.12"

  - script: |
      python -m pip install --upgrade pip
      python -m pip install build twine pytest
      pytest
      python -m build
    displayName: Test and Build

  - task: TwineAuthenticate@1
    inputs:
      artifactFeed: artifacts-project/cloudnautic-feed

  - script: |
      python -m twine upload \
        --config-file "$(PYPIRC_PATH)" \
        -r cloudnautic-feed \
        dist/*
    displayName: Publish Package
```

The exact repository name used with `-r` should match the name generated in the authenticated `.pypirc` file.

---

# 22. Consume Python Package from Feed

Create a virtual environment:

```bash
python -m venv venv
```

Activate on macOS/Linux:

```bash
source venv/bin/activate
```

Activate on Windows:

```powershell
venv\Scripts\activate
```

Open:

```text
Artifacts
    -> cloudnautic-feed
    -> Connect to Feed
    -> Python
    -> pip
```

Install the Azure Artifacts keyring:

```bash
pip install artifacts-keyring
```

Install the package using the feed URL:

```bash
pip install \
  --index-url https://pkgs.dev.azure.com/cloudnautic/artifacts-project/_packaging/cloudnautic-feed/pypi/simple/ \
  cloudnautic-calculator
```

Test:

```python
from cloudnautic_calculator import add

result = add(10, 20)
print(result)
```

Expected output:

```text
30
```

---

# 23. Training Lab 3 — npm Package

## 23.1 Project Structure

```text
cloudnautic-npm-package/
├── index.js
├── package.json
├── .npmrc
└── azure-pipelines.yml
```

---

## 23.2 `index.js`

```javascript
function greet(name) {
  return `Hello ${name}, welcome to Cloudnautic`;
}

module.exports = {
  greet
};
```

---

## 23.3 `package.json`

```json
{
  "name": "cloudnautic-greetings",
  "version": "1.0.0",
  "description": "Azure Artifacts npm training package",
  "main": "index.js",
  "scripts": {
    "test": "node -e \"console.log(require('./index').greet('Azure Student'))\""
  },
  "author": "Atul Kamble",
  "license": "MIT"
}
```

Test:

```bash
npm test
```

---

# 24. Configure npm Feed

Open:

```text
Artifacts
    -> cloudnautic-feed
    -> Connect to Feed
    -> npm
```

Copy the generated project `.npmrc` configuration.

Example:

```ini
registry=https://pkgs.dev.azure.com/cloudnautic/artifacts-project/_packaging/cloudnautic-feed/npm/registry/

always-auth=true
```

The recommended `.npmrc` details should be copied from **Connect to Feed**, because the exact URL depends on feed scope.

---

# 25. Publish npm Package Manually

Authenticate according to the **Connect to Feed** instructions.

Publish:

```bash
npm publish
```

Verify:

```text
Artifacts
    -> cloudnautic-feed
    -> cloudnautic-greetings
    -> 1.0.0
```

Azure Artifacts supports publishing npm packages from the command line and through Azure Pipelines.

---

# 26. Publish npm Package Using Azure Pipeline

```yaml
trigger:
  - main

pool:
  vmImage: ubuntu-latest

steps:
  - task: NodeTool@0
    displayName: Install Node.js
    inputs:
      versionSpec: "22.x"

  - script: |
      node --version
      npm --version
      npm install
      npm test
    displayName: Install and Test

  - task: npmAuthenticate@0
    displayName: Authenticate with Azure Artifacts
    inputs:
      workingFile: .npmrc

  - script: |
      npm publish
    displayName: Publish npm Package
```

Microsoft currently recommends using `npmAuthenticate` for feed authentication. The older `Npm@1` task remains available but is no longer under active feature development.

---

# 27. Use Build ID as npm Package Version

Running the same pipeline with `1.0.0` repeatedly will fail because that version already exists.

Update the version during the pipeline:

```yaml
trigger:
  - main

pool:
  vmImage: ubuntu-latest

steps:
  - task: NodeTool@0
    inputs:
      versionSpec: "22.x"

  - script: |
      npm version 1.0.$(Build.BuildId) --no-git-tag-version
      cat package.json
    displayName: Update Package Version

  - task: npmAuthenticate@0
    inputs:
      workingFile: .npmrc

  - script: |
      npm publish
    displayName: Publish npm Package
```

Example:

```text
Build ID: 45
Package version: 1.0.45
```

---

# 28. Consume npm Package from Azure Artifacts

Configure `.npmrc`:

```ini
registry=https://pkgs.dev.azure.com/cloudnautic/artifacts-project/_packaging/cloudnautic-feed/npm/registry/

always-auth=true
```

Install:

```bash
npm install cloudnautic-greetings
```

Use the package:

```javascript
const { greet } = require("cloudnautic-greetings");

console.log(greet("Atul"));
```

Run:

```bash
node app.js
```

Expected output:

```text
Hello Atul, welcome to Cloudnautic
```

---

# 29. Consume npm Package Using Azure Pipeline

```yaml
trigger:
  - main

pool:
  vmImage: ubuntu-latest

steps:
  - task: NodeTool@0
    inputs:
      versionSpec: "22.x"

  - task: npmAuthenticate@0
    inputs:
      workingFile: .npmrc

  - script: |
      npm install
      npm test
    displayName: Install Packages and Test
```

---

# 30. Upstream Source Practical Lab

## Objective

Install a public npm package through an Azure Artifacts feed and confirm that Azure Artifacts saves it.

## Step 1: Enable npm Upstream

Open:

```text
Artifacts
    -> cloudnautic-feed
    -> Feed Settings
    -> Upstream Sources
```

Add:

```text
npmjs
```

---

## Step 2: Give User Collaborator Permission

Open:

```text
Feed Settings
    -> Permissions
```

Assign:

```text
Feed and Upstream Reader (Collaborator)
```

---

## Step 3: Configure `.npmrc`

```ini
registry=https://pkgs.dev.azure.com/cloudnautic/artifacts-project/_packaging/cloudnautic-feed/npm/registry/

always-auth=true
```

---

## Step 4: Install a Public Package

```bash
npm install lodash
```

Flow:

```text
npm install lodash
        |
        v
cloudnautic-feed
        |
        v
npmjs.com
        |
        v
Package saved in cloudnautic-feed
```

Once an upstream package is installed successfully, Azure Artifacts automatically saves that package version in the feed.

---

## Step 5: Verify

Open:

```text
Artifacts
    -> cloudnautic-feed
```

Search:

```text
lodash
```

The package should now appear as an upstream-saved package.

---

# 31. Package Promotion

A newly published package appears in:

```text
@Local
```

After testing, promote it to:

```text
@Prerelease
```

After approval, promote it to:

```text
@Release
```

## Steps

1. Open **Artifacts**.
2. Select the feed.
3. Select the package.
4. Select the required version.
5. Select **Promote**.
6. Select:

```text
@Prerelease
```

or:

```text
@Release
```

A user must be a Feed Publisher or Feed Owner to promote packages.

---

# 32. Delete a Package

## Portal Steps

1. Open Azure DevOps.
2. Open the project.
3. Select **Artifacts**.
4. Select the feed.
5. Select the package.
6. Select the package version.
7. Select **Delete**.
8. Confirm deletion.

The package version moves to the Recycle Bin and becomes unavailable for normal installation or download.

---

# 33. Different Package Removal Options

Different package formats have different soft-removal options.

| Package Type      | Alternative Action |
| ----------------- | ------------------ |
| npm               | Deprecate          |
| NuGet             | Unlist             |
| Cargo             | Yank               |
| Python            | Delete             |
| Maven             | Delete             |
| Universal Package | Delete             |

## npm Deprecate

```bash
npm deprecate cloudnautic-greetings@1.0.0 \
  "This version contains an issue. Use version 1.0.1."
```

## npm Delete

```bash
npm unpublish cloudnautic-greetings@1.0.0
```

Deprecation keeps the version available but displays a warning. Deletion moves it to the Recycle Bin.

---

# 34. Recover a Deleted Package

Deleted packages remain in the Recycle Bin for 30 days before automatic permanent deletion. Only a Feed Owner can restore them.

## Recovery Steps

1. Open **Azure DevOps**.
2. Open the project.
3. Select **Artifacts**.
4. Select **Recycle Bin**.
5. Select the deleted package.
6. Select **Restore**.
7. Confirm restoration.

After restoration:

```text
Recycle Bin -> Original Feed
```

---

# 35. Permanently Delete a Package

1. Open **Artifacts**.
2. Select the feed.
3. Open **Recycle Bin**.
4. Select the package.
5. Select **Permanently Delete**.
6. Confirm.

Permanently deleted packages cannot be restored.

Packages in the Recycle Bin continue counting toward Azure Artifacts storage until they are permanently deleted or the 30-day period expires.

---

# 36. Important Version Rule After Deletion

Suppose this version was published:

```text
cloudnautic-tools 1.0.0
```

It was then deleted.

You still cannot publish another package using:

```text
cloudnautic-tools 1.0.0
```

Use a new version:

```text
cloudnautic-tools 1.0.1
```

Azure Artifacts permanently reserves previously published package versions to maintain package integrity.

---

# 37. Retention Policies

Retention policies automatically remove older package versions.

## Configure Retention

1. Open **Artifacts**.
2. Select the feed.
3. Open **Feed Settings**.
4. Select **Retention Policies**.
5. Enable retention.
6. Configure:

   * Maximum versions per package
   * Days to keep recently downloaded packages
7. Save.

Example:

```text
Maximum package versions: 10
Keep recently downloaded versions: 30 days
```

A package version must satisfy both configured retention conditions before it is removed. Package versions promoted to a view are exempt from retention deletion.

---

# 38. Pipeline Artifact vs Azure Artifacts Package

These are different concepts.

| Pipeline Artifact                   | Azure Artifacts Package              |
| ----------------------------------- | ------------------------------------ |
| Output of a pipeline run            | Versioned reusable package           |
| Usually temporary                   | Designed for long-term reuse         |
| Connected to a build                | Stored inside a feed                 |
| Used between stages/jobs            | Used across projects/applications    |
| Examples: logs, ZIP, compiled files | npm, NuGet, Maven, Python, Universal |

## Pipeline Artifact Example

```yaml
- task: PublishPipelineArtifact@1
  inputs:
    targetPath: "$(Build.ArtifactStagingDirectory)"
    artifact: "application-build"
```

## Azure Artifacts Universal Package Example

```yaml
- task: UniversalPackages@0
  inputs:
    command: publish
    publishDirectory: package
    feedsToUsePublish: internal
    vstsFeedPublish: artifacts-project/cloudnautic-feed
    vstsFeedPackagePublish: cloudnautic-tools
    versionOption: custom
    versionPublish: 1.0.$(Build.BuildId)
```

---

# 39. Complete CI Pipeline Example

```yaml
trigger:
  - main

pool:
  vmImage: ubuntu-latest

variables:
  packageName: cloudnautic-tools
  packageVersion: 1.0.$(Build.BuildId)
  feedName: artifacts-project/cloudnautic-feed

stages:
  - stage: Test
    displayName: Test Application
    jobs:
      - job: TestApplication
        steps:
          - task: UsePythonVersion@0
            inputs:
              versionSpec: "3.12"

          - script: |
              python package/app.py
            displayName: Test Python Application

  - stage: Package
    displayName: Publish Package
    dependsOn: Test
    jobs:
      - job: PublishPackage
        steps:
          - script: |
              echo "Package: $(packageName)"
              echo "Version: $(packageVersion)"
              find package -maxdepth 2 -type f
            displayName: Verify Package

          - task: UniversalPackages@0
            displayName: Publish to Azure Artifacts
            inputs:
              command: publish
              publishDirectory: package
              feedsToUsePublish: internal
              vstsFeedPublish: $(feedName)
              vstsFeedPackagePublish: $(packageName)
              versionOption: custom
              versionPublish: $(packageVersion)
              packagePublishDescription: Package from Azure Pipeline build $(Build.BuildId)

  - stage: Consume
    displayName: Verify Published Package
    dependsOn: Package
    jobs:
      - job: ConsumePackage
        steps:
          - task: UniversalPackages@0
            displayName: Download Published Package
            inputs:
              command: download
              downloadDirectory: $(Pipeline.Workspace)/downloaded
              feedsToUse: internal
              vstsFeed: $(feedName)
              vstsFeedPackage: $(packageName)
              vstsPackageVersion: $(packageVersion)

          - script: |
              echo "Downloaded files:"
              find "$(Pipeline.Workspace)/downloaded" -maxdepth 3 -type f

              python "$(Pipeline.Workspace)/downloaded/app.py"
            displayName: Test Downloaded Package
```

---

# 40. Troubleshooting

## Error 1: Package Already Exists

```text
The package version already exists
```

Cause:

```text
The same package version was published previously.
```

Solution:

```yaml
versionPublish: 1.0.$(Build.BuildId)
```

---

## Error 2: User Is Not Authorized

```text
401 Unauthorized
```

Possible causes:

* Feed authentication is missing
* PAT is invalid
* `.npmrc` is incorrect
* `.pypirc` is incorrect
* Credential provider is missing

Solution:

* Open **Connect to Feed**
* Copy the latest configuration
* Run authentication task
* Verify token permissions

---

## Error 3: Pipeline Cannot Publish

```text
403 Forbidden
```

Cause:

```text
Build Service does not have Feed Publisher permission.
```

Solution:

```text
Feed Settings
    -> Permissions
    -> Add Build Service
    -> Feed Publisher (Contributor)
```

---

## Error 4: Upstream Package Is Not Saved

Possible causes:

* Upstream source is disabled
* User has only Feed Reader permission
* Package source is incorrectly configured

Solution:

```text
Assign Feed and Upstream Reader (Collaborator)
```

---

## Error 5: Package Not Found

```text
404 Package not found
```

Check:

```text
Feed name
Package name
Package version
Project name
Feed scope
Selected feed view
```

---

## Error 6: Same Version Cannot Be Republished After Deletion

Cause:

```text
Azure Artifacts permanently reserves the published version.
```

Solution:

```text
Old: 1.0.0
New: 1.0.1
```

---

## Error 7: Feed Storage Did Not Reduce

Cause:

```text
Deleted packages remain in the Recycle Bin and still use storage.
```

Solution:

```text
Artifacts
    -> Recycle Bin
    -> Permanently Delete
```

---

# 41. Best Practices

## Package Versioning

Use semantic versioning:

```text
MAJOR.MINOR.PATCH
```

Example:

```text
1.0.0
```

Increase MAJOR:

```text
2.0.0
```

Use when making incompatible changes.

Increase MINOR:

```text
1.1.0
```

Use when adding backward-compatible features.

Increase PATCH:

```text
1.0.1
```

Use for bug fixes.

---

## Recommended Feed Design

```text
company-development-feed
company-shared-feed
company-production-feed
```

Alternatively, use one feed with views:

```text
@Local
@Prerelease
@Release
```

---

## Security Practices

* Never commit PATs
* Never commit `.env`
* Never store passwords inside packages
* Use pipeline secret variables
* Apply least-privilege permissions
* Restrict package publishing
* Review public-feed visibility
* Use approved upstream sources
* Promote tested packages to `@Release`

---

## Pipeline Practices

* Use automated package versions
* Test before publishing
* Publish only from approved branches
* Use pull-request validation
* Separate build and publish stages
* Publish release packages only after approval
* Use package descriptions
* Configure retention policies
* Use `.artifactignore`

---

# 42. Recommended Git Workflow

```bash
git init
git add .
git commit -m "add Azure Artifacts package practice"
git branch -M main
git remote add origin https://github.com/atulkamble/azure-artifacts-practice.git
git push -u origin main
```

---

# 43. Suggested Repository Names

```text
azure-artifacts-practice
```

Other options:

```text
azure-artifacts-zero-to-hero
azure-artifacts-pipeline-lab
azure-package-management-practice
azure-artifacts-universal-package
azure-artifacts-python-npm-lab
```

Recommended:

```text
azure-artifacts-practice
```

---

# 44. Student Assignments

## Assignment 1

Create a private Azure Artifacts feed:

```text
student-private-feed
```

Publish a Universal Package:

```text
student-tools
Version: 1.0.0
```

---

## Assignment 2

Create an Azure Pipeline that publishes:

```text
student-tools
Version: 1.0.$(Build.BuildId)
```

---

## Assignment 3

Create a second pipeline that downloads and executes the package.

---

## Assignment 4

Enable npm upstream sources and install:

```bash
npm install lodash
```

Verify that the package appears in the feed.

---

## Assignment 5

Delete one package version and recover it from the Recycle Bin.

---

## Assignment 6

Create a Python calculator package with:

```python
add()
subtract()
multiply()
divide()
```

Publish it to Azure Artifacts and consume it in another project.

---

## Assignment 7

Create the following package promotion flow:

```text
@Local
   |
   v
@Prerelease
   |
   v
@Release
```

---

# 45. Interview Questions

## Basic

1. What is Azure Artifacts?
2. What is a feed?
3. Which package formats are supported?
4. What is a Universal Package?
5. What is package versioning?
6. What is a project-scoped feed?
7. What is an organization-scoped feed?
8. What is a private feed?
9. What is a public feed?
10. What is an upstream source?

## Intermediate

11. Why are upstream sources useful?
12. What happens when a package is obtained from an upstream source?
13. What is the Feed Publisher role?
14. What is the Collaborator role?
15. How does an Azure Pipeline authenticate with a feed?
16. What is `UniversalPackages@0`?
17. What is `npmAuthenticate@0`?
18. What is `TwineAuthenticate@1`?
19. What is `.artifactignore`?
20. What are feed views?

## Advanced

21. Can the same package version be overwritten?
22. Can a deleted package version number be reused?
23. How long does a deleted package remain recoverable?
24. Do packages in the Recycle Bin consume storage?
25. How do retention policies work?
26. Why should build IDs be used in package versions?
27. What is the difference between a pipeline artifact and an Azure Artifacts package?
28. How do you provide cross-project feed access?
29. How can package promotion support release management?
30. What happens when a public upstream registry is unavailable?

---

# 46. Quick Revision

```text
Azure Artifacts
    |
    +-- Feed
    |
    +-- Package
    |
    +-- Version
    |
    +-- Permissions
    |
    +-- Upstream Sources
    |
    +-- Feed Views
    |
    +-- Publish
    |
    +-- Consume
    |
    +-- Delete
    |
    +-- Restore
```

## Complete Package Lifecycle

```text
Create Code
    |
    v
Build Package
    |
    v
Test Package
    |
    v
Publish to Feed
    |
    v
Package appears in @Local
    |
    v
Promote to @Prerelease
    |
    v
Test and Approve
    |
    v
Promote to @Release
    |
    v
Consume in Application
    |
    v
Retain, Deprecate or Delete
```

---

# 47. Final Summary

Azure Artifacts provides centralized package management inside Azure DevOps.

It supports:

```text
npm
NuGet
Maven
Python
Cargo
Universal Packages
```

Main operations:

```text
Create Feed
Configure Permissions
Configure Upstream Sources
Build Package
Publish Package
Consume Package
Promote Package
Delete Package
Recover Package
Manage Retention
```

Recommended training workflow:

```text
GitHub/Azure Repos
        |
        v
Azure Pipeline
        |
        v
Build and Test
        |
        v
Azure Artifacts Feed
        |
        v
Package Consumer Pipeline
        |
        v
Application Deployment
```
