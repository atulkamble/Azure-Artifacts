# 🎯 **Azure Artifacts **

![Image](https://learn.microsoft.com/en-us/azure/devops/artifacts/media/package-created-confirmation.png?view=azure-devops\&utm_source=chatgpt.com)

![Image](https://azure.microsoft.com/en-us/blog/wp-content/uploads/2018/09/f6b58049-3b73-46c7-83cd-338cea0474e0.webp?utm_source=chatgpt.com)

Azure Artifacts supports:
✔ **npm**
✔ **Python (pip/twine)**
✔ **Maven**
✔ **NuGet (.NET)**
✔ **Universal Packages**
✔ **Cargo (Rust)**

Below you get **super simple publish + consume examples**.

---

# 🟦 1. **npm Basic Code**

### **1️⃣ Create package**

```bash
mkdir my-npm-app
cd my-npm-app
npm init -y
```

### **2️⃣ Create basic JS file**

`index.js`

```javascript
module.exports = function(){
  console.log("Hello from Azure Artifacts npm package!");
}
```

### **3️⃣ Login to Azure Artifacts feed**

```bash
npm login --registry=https://pkgs.dev.azure.com/<ORG>/<PROJECT>/_packaging/<FEED>/npm/registry/
```

### **4️⃣ Publish package**

```bash
npm publish
```

---

# 🟦 2. **Python (pip) Basic Code**

### **1️⃣ Create package**

```bash
mkdir mypyapp
cd mypyapp
```

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

### **2️⃣ Build**

```bash
python3 setup.py sdist bdist_wheel
```

### **3️⃣ Upload to Azure Artifacts**

```bash
twine upload --repository-url https://pkgs.dev.azure.com/<ORG>/<PROJECT>/_packaging/<FEED>/pypi/upload dist/*
```

---

# 🟦 3. **Maven Basic Code**

`pom.xml`

```xml
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.demo</groupId>
  <artifactId>hello-artifacts</artifactId>
  <version>1.0.0</version>
</project>
```

### **Publish**

Configure `settings.xml` with feed URL:

```xml
<server>
  <id>azure-artifacts</id>
  <username>Azure DevOps</username>
  <password><YOUR_PAT></password>
</server>
```

Publish:

```bash
mvn deploy
```

---

# 🟦 4. **NuGet (.NET) Basic Code**

### **1️⃣ Create project**

```bash
dotnet new classlib -n HelloArtifacts
cd HelloArtifacts
```

`Class1.cs`

```csharp
namespace HelloArtifacts;
public class Greet {
    public string SayHello() => "Hello from Azure Artifacts NuGet!";
}
```

### **2️⃣ Pack**

```bash
dotnet pack -o out
```

### **3️⃣ Push**

```bash
dotnet nuget push out/*.nupkg \
  --source "https://pkgs.dev.azure.com/<ORG>/<PROJECT>/_packaging/<FEED>/nuget/v3/index.json" \
  --api-key <YOUR_PAT>
```

---

# 🟦 5. **Universal Packages (Any File)**

### Create a file

```bash
echo "hello universal package" > demo.txt
```

### Publish

```bash
az artifacts universal publish \
  --organization https://dev.azure.com/<ORG> \
  --feed <FEED> \
  --name demo \
  --version 1.0.0 \
  --path .
```

### Download

```bash
az artifacts universal download \
  --organization https://dev.azure.com/<ORG> \
  --feed <FEED> \
  --name demo \
  --version 1.0.0 \
  --path .
```

---

# 🟦 6. **Cargo (Rust) Basic Code**

`src/lib.rs`

```rust
pub fn greet() -> String {
    "Hello from Azure Artifacts Cargo package!".to_string()
}
```

### Add feed URL in `.cargo/config`

```toml
[registries.azure]
index = "https://pkgs.dev.azure.com/<ORG>/<PROJECT>/_packaging/<FEED>/cargo/index"
```

Publish:

```bash
cargo publish --registry azure
```

---
