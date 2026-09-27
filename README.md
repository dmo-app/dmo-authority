# DMO Authority (The Brain)

This repository is the single source of truth for the **Business Rules**, **Scope**, and **Identities** of the DMO application.

### The Ecosystem
*   🧠 **`dmo-authority` (This Repo):** Defines **WHAT** we build and the **RULES** we follow.
*   ⚙️ **`dmo-app-beta`:** The implementation (Code/DB). Must obey this repo.
*   🎨 **`dmo-design`:** The visual lab (UI/Prototypes). Must obey this repo.

### Core Principle
**"The Beta is a Scope-Reduced Product."**
It is not a "test version" of the full app. It is a distinct product with strict boundaries. If a module is not in `SCOPE.md`, it is **FORBIDDEN** to design, code, or document it.
