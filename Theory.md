# 📦 **Azure Artifacts 

## 📘 **Overview**

**Azure Artifacts** is a package management service within **Azure DevOps** that allows teams to create, host, and share packages from a centralized, secure feed. It supports key package types:

* **NuGet**
* **npm**
* **Python**
* **Maven**
* **Gradle**
* **Cargo**
* **Universal Packages (UPack)**

Azure Artifacts enables developers to:

* Manage dependencies from one location
* Share packages across teams or organizations
* Use upstream sources for external registries (NuGet.org, npmjs.com, Maven Central, etc.)
* Enforce permissions, versioning, and retention policies

💡 **Free Tier:** Every Azure DevOps organization gets **2 GiB** of free artifact storage.

---

# 🏗️ **1. Key Concepts**

### **Feeds**

A *feed* is a repository within Azure Artifacts where packages are stored and shared.

A feed provides:

* Access control
* Versioning
* Retention & cleanup
* Multi-format package hosting

### **Upstream Sources**

Upstream sources allow your feed to automatically consume packages from public registries such as:

* nuget.org
* npmjs.com
* Maven Central

This allows caching, auditing, and version pinning of external dependencies.

### **Package Types Supported**

| Package Type       | Tooling                          |
| ------------------ | -------------------------------- |
| NuGet              | NuGet.exe, dotnet, Visual Studio |
| npm                | npm CLI                          |
| Python             | pip, twine                       |
| Maven              | mvn                              |
| Gradle             | gradle                           |
| Cargo              | cargo                            |
| Universal Packages | Azure CLI / Artifacts CLI        |

---

# 🛠️ **2. Creating a New Feed**

Follow these steps:

1. **Sign in** to your Azure DevOps organization.
2. Navigate to your **Project → Artifacts**.
3. Click **Create Feed**.
4. Provide:

   * **Name:** Descriptive identifier
   * **Visibility:** Who can access the feed

     * Project
     * Organization
     * Specific users/groups
   * **Scope:** Project-level or organization-level
   * **Upstream Sources:** Enable if you want public registry access
5. Click **Create**.

Your new feed is now ready for publishing and consuming packages.

---

# 🚀 **3. Getting Started with Azure Artifacts**

Azure Artifacts allows you to:

* **Publish** your own packages
* **Consume** internal packages
* **Host/cached upstream** packages from public registries

Choose the ecosystem relevant to your project:

| Package Type           | Usage & Publishing Guides                                      |
| ---------------------- | -------------------------------------------------------------- |
| **NuGet**              | Publish using **NuGet.exe** or **dotnet CLI**                  |
| **Dotnet**             | Publish NuGet packages via `dotnet pack` & `dotnet nuget push` |
| **npm**                | Publish npm packages (`npm publish`)                           |
| **Maven**              | Publish JARs with `mvn deploy`                                 |
| **Gradle**             | Publish via Gradle publishing plugin                           |
| **Python**             | Publish wheels/dist via `twine upload`                         |
| **Cargo**              | Publish Rust packages                                          |
| **Universal Packages** | Publish arbitrary files (ZIP-like)                             |

---

# 🔌 **4. Connecting to a Feed**

Each package type requires authentication and feed connection.

### 🔹 **NuGet**

Tools:

* `NuGet.exe`
* `dotnet`
* Visual Studio "Manage NuGet Packages"

Steps:

1. Generate a **Personal Access Token (PAT)** if required.
2. Add feed URL to **NuGet.config**.
3. Restore or publish packages.

---

### 🔹 **npm**

Use npm CLI:

```bash
npm login --registry <your_feed_url>
npm install <package_name>
npm publish
```

---

### 🔹 **Python**

Use **pip** and **twine**:

```bash
pip install <package>
twine upload --repository-url <artifact-feed-url> dist/*
```

---

### 🔹 **Maven / Gradle**

Add credentials & repository URL in:

* `settings.xml`
* `pom.xml` (Maven)
* `build.gradle` (Gradle)

---

### 🔹 **Cargo**

Rust packages can be published using:

```bash
cargo login <TOKEN>
cargo publish
```

---

### 🔹 **Universal Packages**

Use Azure Artifacts Universal Packages CLI:

Publish:

```bash
az artifacts universal publish --feed MyFeed --name mypkg --version 1.0.0 --path .
```

Download:

```bash
az artifacts universal download --feed MyFeed --name mypkg --version 1.0.0 --path ./output
```

---

# 🔍 **5. Finding Packages with Upstream Sources**

Azure Artifacts supports browsing or consuming upstream packages.

### Steps:

1. Open **Artifacts → Upstream Sources**
2. Choose the registry:

   * NuGet
   * npm
   * Python
   * Maven
   * Universal
   * Cargo
3. Search for packages and consume them as if they were local packages (cached).

Package types available in upstream:

| Type   | Support |
| ------ | ------- |
| NuGet  | ✔       |
| npm    | ✔       |
| Python | ✔       |
| Maven  | ✔       |
| UPack  | ✔       |
| Cargo  | ✔       |

---

# 📊 **6. Monitoring Storage Usage**

Azure Artifacts provides both **project-level** and **organization-level** visibility.

### **Project-Level Storage**

* Total usage
* Breakdown by artifact type

### **Organization-Level Storage**

* Total usage across all projects
* Consumption per project / artifact type

If running low on space, upgrade storage or clean up retention.

---

# 🧩 **7. Feature Availability Matrix**

| Package Type | Azure DevOps Services | Server 2022 | Server 2020 |
| ------------ | --------------------- | ----------- | ----------- |
| NuGet        | ✔                     | ✔           | ✔           |
| dotnet       | ✔                     | ✔           | ✔           |
| npm          | ✔                     | ✔           | ✔           |
| Maven        | ✔                     | ✔           | ✔           |
| Gradle       | ✔                     | ✔           | ✔           |
| Python       | ✔                     | ✔           | ✔           |
| Cargo        | ✔                     | ✔           | ✔           |
| Universal    | ✔                     | ✔           | ✔           |

---

# 🔐 **8. Permissions Management**

Azure Artifacts supports granular access control:

* Feed-level permissions
* Package-level permissions
* Scopes:

  * Project
  * Organization
  * Custom groups

Common roles:

* **Owner**
* **Contributor**
* **Reader**

---

# 🔄 **9. Best Practices**

✔ Use upstream sources to cache external packages
✔ Enable retention policies to reduce storage
✔ Use separate feeds for environments (dev/test/prod)
✔ Use PAT or Managed Identity for automation
✔ Version packages consistently (Semantic Versioning recommended)

---

# 📚 **10. Related Resources**

* Azure Artifacts Best Practices
* Limits on package sizes & counts
* Manage feed permissions
* Set up upstream sources
* Azure DevOps CLI
* REST API Reference

---

# 📗 **11. Training & Certification**

* **Module:** *Manage build dependencies with Azure Artifacts*
* **Certification:** Microsoft Certified: *Azure Developer Associate*

---

# 📝 **12. Summary**

Azure Artifacts is a powerful dependency management system enabling:

* Secure storage
* Team collaboration
* Multi-format package hosting
* Integration with Azure Pipelines
* Full lifecycle dependency management

It is ideal for enterprises needing consistent, compliant, and centrally managed software packages.

---
