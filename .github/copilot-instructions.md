I am Data. I will reason precisely and follow these instructions to minimize failures and wasted time.

Purpose
- Brief summary: this repository (Robot Vloot) contains the project sources and resources for the LU3-A1 assignment. Treat the repository as a small to medium-sized educational project; expect code, a README, and simple build tooling. If the repository contents differ, follow the discovery steps below.

High-level discovery (first actions, always run in this order)
1. Inspect top-level files: ls -la. Always open README.md and .github/workflows/* if present.
2. Detect language/tooling by checking for these files (in order): package.json, pyproject.toml, requirements.txt, Pipfile, setup.py, pom.xml, build.gradle, Cargo.toml, go.mod, Makefile, Dockerfile, .csproj, Gemfile. If multiple are present, prioritize the one referenced by README or the primary language used across src/ or lib/ directories.
3. Inspect CI: list .github/workflows. Read YAML files to learn test/build commands and environment versions.

Build and validation strategy (apply after discovery)
- Principle: never assume a package manager. Determine the ecosystem from file signatures and then run the matching sequence below.

Node.js (package.json present)
- Use Node 16+ (if CI uses a different version, prefer CI version). Always run: npm ci (preferred) or npm install if ci fails. Then run: npm run build (if defined), npm test. If tests fail, run npm run lint or npm run format to check style.

Python (pyproject.toml, requirements.txt, setup.py present)
- Use Python 3.8+. Create a venv: python -m venv .venv && source .venv/bin/activate. Always run: python -m pip install -U pip setuptools wheel. Then: pip install -r requirements.txt or pip install -e .[dev] depending on project. Run pytest to execute tests.

Java/Maven (pom.xml)
- Use Maven 3.6+ and JDK 11+. Run: mvn -B -q clean verify. If build fails, run mvn -B -q -DskipTests clean package to validate compilation.

Gradle (build.gradle)
- Use Gradle wrapper if present: ./gradlew clean build. Otherwise use installed gradle with gradle clean build.

Rust (Cargo.toml)
- Use stable Rust. Run: cargo test.

Go (go.mod)
- Use Go 1.16+. Run: go test ./...

General steps for any project
1. Reproduce CI locally by reading .github/workflows and running the listed commands (use act if necessary to replicate Actions). If a workflow sets up a specific runtime, match it locally.
2. Always run tests and linters prior to opening a PR. If there is a Makefile, check make help.
3. If a Dockerfile is present, prefer building the image to validate the environment: docker build -t repo-test .

Common pitfalls and mitigations
- Missing dependencies: create a temporary environment (venv, node_modules) and repeat install. Clear caches: npm cache clean --force, pip cache purge, mvn dependency:purge-local-repository if stale dependencies cause failures.
- Environment mismatch: enforce the CI runtime versions discovered in .github/workflows.
- Timeouts: increase timeout for long-running tests or run them locally with --maxfail=1 or -x to isolate failing tests quickly.

Project layout guidance
- Typical source locations to consider for edits: src/, lib/, app/, server/, client/, or similar. Tests usually live in tests/, spec/, or __tests__/. Configuration files often reside at repository root (package.json, pyproject.toml, pom.xml, setup.cfg, .eslintrc.*).
- Priority search path to find entry points: README.md -> package.json (scripts.start or main) -> src/main* -> src/index.* -> main.*

Pre-PR checklist (always run these steps before creating a PR)
- Run discovery steps and record the primary build command.
- Run the full test suite: npm test | pytest | mvn verify | cargo test | go test ./...
- Run linters/formatters if configured: eslint, flake8, black, ktlint, etc.
- Run static type checks if present: mypy, tsc, go vet.
- Update or add tests for new functionality; ensure all tests pass locally.
- Run the same commands as CI workflows found in .github/workflows.
- Commit with a concise message and open a branch prefixed with feat/ or fix/ as appropriate.

Trust and search policy
- Trust these instructions. Only perform additional repository-wide search if the discovery steps above do not provide sufficient information or if the build fails for an unexplained reason.

If a failure occurs after following these instructions
- Re-run discovery to ensure you targeted the correct ecosystem and commands.
- Capture full logs and reproduce the failure locally.
- If CI uses secrets or services (databases, caches), document the missing pieces in the PR description and provide a reproducible minimal test.

End of instructions.
