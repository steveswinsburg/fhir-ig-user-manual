# FHIR IG User Manual
A user manual for creating, editing and publishing a FHIR IG

## 1. Introduction
FHIR Implementation Guides (IGs) define how FHIR resources should be used in a specific domain or jurisdiction. This guide will help you set up, create, and build a FHIR IG, add new profiles, define datatypes and extensions, and understand how the content is generated and linked.

## 2. Prerequisites
Before you begin, ensure you have the following installed:

- **Java (JDK 11+)** – Required for running the FHIR IG Publisher.
- **Git** – Version control system for managing your IG.
- **Ruby and jekyll** (optional) – Required if publishing the IG manually through raw XML and JSON.
- **Homebrew** (optional) - For MacOS only and only if publishing via jekyll. You can also use a different package manager if you prefer.
- **Node.js & npm** (optional) – Required if using SUSHI for defining the IG.
- **FHIR IG Publisher** – Used to build and validate the IG.
- **FHIR Toolkit** (optional) – For additional validation and testing.

## 3. Setting Up Your IG Project
FHIR IGs are structured projects following a standard format. The easiest way to get started is:

### a) Clone the Official IG Template Repository
```sh
git clone https://github.com/FHIR/sample-ig.git my-ig
cd my-ig
```

### b) Initialize Your IG
Modify `ig.ini` to specify your IG's unique identifier and FHIR version.

### c) Install SUSHI (Optional, only if using FHIR Shorthand)
SUSHI is a tool to define FHIR profiles in a more human-readable format.
```sh
npm install -g fsh-sushi
```

### c) Install Ruby and jekyll (Optional, only if manually publishing)
Manual XML and JSON is built using jekyll, which is a Ruby gem.
Read up on the jekyll install here: https://jekyllrb.com/docs/installation

#### MacOS:

**Install Ruby**

Install a separate version of Ruby using Homebrew. The version of Ruby that comes with MacOS is not designed for extension.
you can use a different package manager if you prefer, the steps are roughly the same.

```
brew install rbenv
rbenv init
```

List available Ruby versions:
```
rbenv install -l
rbenv install 3.4.2
```

Activate it:
```
rbenv global 3.4.2
```

This may add it to .zprofile, move it to .zshrc if you want.

Reopen terminal so the changes take effect.

**Install jekyll**

```
gem install jekyll
```

#### Windows:

**Install Ruby**

Download the latest stable version (x64) from https://rubyinstaller.org/downloads/
Run the installer and ensure the devkit is installed also.

Verify:
```
ruby -v
```

**Install Jekyll**

```
gem install jekyll
```

## 4. Understanding the IG Directory Structure
Your IG project will have the following key folders:

- `input/fsh/` – FHIR Shorthand (FSH) definitions for profiles, extensions, value sets, etc.
- `input/resources/` – JSON or XML files defining FHIR resources.
- `input/pagecontent/` – Markdown (`.md`) or HTML files for documentation pages.
- `input/examples/` – Example instances of your FHIR resources.
- `fsh-generated/` – Auto-generated FHIR resources from FSH definitions (if using SUSHI).
- `output/` – Generated website and resources after running the IG Publisher.

## 5. Creating and Adding New Profiles
Profiles allow you to customise FHIR resources to meet specific requirements.

### a) Using JSON Directly
Create a `StructureDefinition` JSON file in `input/resources/`:
```json
{
  "resourceType": "StructureDefinition",
  "id": "MyPatientProfile",
  "url": "http://example.org/fhir/StructureDefinition/MyPatientProfile",
  "name": "MyPatientProfile",
  "status": "draft",
  "kind": "resource",
  "type": "Patient",
  "baseDefinition": "http://hl7.org/fhir/StructureDefinition/Patient",
  "derivation": "constraint"
}
```

### b) Using FHIR Shorthand (FSH)
Define your profile in `input/fsh/`:
```fsh
Profile: MyPatientProfile
Parent: Patient
Id: my-patient-profile
Title: "My Patient Profile"
Description: "A custom profile for Patient."
* gender = #female (exactly)
```
Then, compile it into JSON using SUSHI:
```sh
sushi .
```

## 6. Creating and Adding Custom DataTypes
FHIR allows the creation of custom datatypes to support specialised use cases.

### a) Defining a Custom DataType
Create a `StructureDefinition` JSON file for your datatype in `input/resources/`:
```json
{
  "resourceType": "StructureDefinition",
  "id": "MyCustomDatatype",
  "url": "http://example.org/fhir/StructureDefinition/MyCustomDatatype",
  "name": "MyCustomDatatype",
  "status": "draft",
  "kind": "complex-type",
  "type": "Element",
  "derivation": "specialization"
}
```

### b) Using FHIR Shorthand (FSH) for DataTypes
Define your datatype in `input/fsh/`:
```fsh
Profile: MyCustomDatatype
Parent: Element
Id: my-custom-datatype
Title: "My Custom Datatype"
Description: "A specialized datatype for my IG."
```

## 7. Creating and Adding Extensions
Extensions allow adding custom fields to standard FHIR resources.

### a) Defining an Extension in JSON
Create a JSON file in `input/resources/`:
```json
{
  "resourceType": "StructureDefinition",
  "id": "MyExtension",
  "url": "http://example.org/fhir/StructureDefinition/MyExtension",
  "name": "MyExtension",
  "status": "draft",
  "kind": "complex-type",
  "type": "Extension",
  "context": [{"type": "element", "expression": "Patient"}]
}
```

### b) Using FHIR Shorthand (FSH) for Extensions
Define your extension in `input/fsh/`:
```fsh
Extension: MyExtension
Id: my-extension
Title: "My Custom Extension"
Description: "An example extension for adding extra fields."
* value[x] only string
```

## 8. Adding Custom Pages
To add documentation pages:

1. Create a new `.md` file in `input/pagecontent/`, e.g., `mypage.md`.
2. Add content using Markdown:
```md
# My Custom Page
This is my custom documentation page in the IG.
```
3. Reference it in `ig.yaml` under `pages:`
```yaml
pages:
  index.md: index.html
  mypage.md: mypage.html
```

## 9. Linking Between Pages
To create links between pages in your IG:

- Use standard Markdown linking syntax:
```md
[Go to My Custom Page](mypage.html)
```
- Reference resource pages using relative links:
```md
[My Patient Profile](StructureDefinition-MyPatientProfile.html)
```

## 10. Building and Validating Your IG
Run the FHIR IG Publisher to generate the IG website and validate your profiles:
```sh
./_genonce.sh   # Linux/macOS
_genonce.bat    # Windows
```

## 11. Publishing the IG
Once validated, publish your IG to a GitHub repository and use [FHIR Build Infrastructure](https://confluence.hl7.org/display/FHIR/IG+Publisher+Documentation) to deploy it.

## 12. Next Steps
- Explore the [FHIR IG Publisher Documentation](https://confluence.hl7.org/display/FHIR/IG+Publisher+Documentation).
- Join the [FHIR Zulip Chat](https://chat.fhir.org/) for community support.
- Validate profiles using the [FHIR Validator](https://confluence.hl7.org/display/FHIR/Using+the+FHIR+Validator).

---
This guide provides a foundation for building a FHIR IG. Feel free to expand with additional customizations as needed.



