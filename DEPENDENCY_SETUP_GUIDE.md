# Dependency Setup Guide - DocGen

Quick start guide for setting up dependencies and security scanning for DocGen.

---

## Table of Contents

1. [Choose Your Stack](#choose-your-stack)
2. [Python Setup](#python-setup)
3. [Node.js Setup](#nodejs-setup)
4. [Rust Setup](#rust-setup)
5. [Security Tools Setup](#security-tools-setup)
6. [CI/CD Configuration](#cicd-configuration)
7. [Daily Workflow](#daily-workflow)

---

## Choose Your Stack

Select ONE of the following based on your preference:

| Stack | Pros | Cons | Best For |
|-------|------|------|----------|
| **Python** | Excellent AI/ML libraries, rapid development | Slower execution, large install size | AI-heavy tools, rapid prototyping |
| **Node.js** | Large ecosystem, fast development, good CLI tools | Callback hell, type safety requires TS | Cross-platform CLIs, web integration |
| **Rust** | Maximum performance, memory safety, single binary | Steeper learning curve, slower compile | Production tools, performance-critical |

**Recommendation:** Start with **Python** for AI tooling, migrate to Rust later if needed.

---

## Python Setup

### 1. Initialize Project

```bash
# Copy template
cp pyproject.toml.template pyproject.toml

# Create virtual environment
python -m venv venv

# Activate (Linux/macOS)
source venv/bin/activate

# Activate (Windows)
.\venv\Scripts\activate

# Verify Python version
python --version  # Should be 3.11+
```

### 2. Install Dependencies

```bash
# Install project in editable mode with dev dependencies
pip install -e ".[dev]"

# Verify installation
pip list
```

### 3. Security Tools

```bash
# Install security scanners
pip install pip-audit safety bandit

# Run vulnerability scan
pip-audit

# Alternative: Safety
safety check

# Static security analysis
bandit -r src
```

### 4. Development Tools

```bash
# Format code
black src

# Lint code
ruff check src

# Fix linting issues automatically
ruff check --fix src

# Type checking
mypy src

# Run tests
pytest

# Run tests with coverage
pytest --cov=docgen --cov-report=html
```

### 5. Pre-commit Hooks (Optional)

```bash
# Install pre-commit
pip install pre-commit

# Create .pre-commit-config.yaml
cat > .pre-commit-config.yaml << 'EOF'
repos:
  - repo: https://github.com/psf/black
    rev: 24.10.0
    hooks:
      - id: black

  - repo: https://github.com/charliermarsh/ruff-pre-commit
    rev: v0.8.0
    hooks:
      - id: ruff
        args: [--fix]

  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v5.0.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-added-large-files
        args: ['--maxkb=1000']
      - id: detect-private-key

  - repo: https://github.com/PyCQA/bandit
    rev: 1.8.0
    hooks:
      - id: bandit
        args: ['-c', 'pyproject.toml']
EOF

# Install hooks
pre-commit install

# Run manually
pre-commit run --all-files
```

### 6. Keep Dependencies Updated

```bash
# List outdated packages
pip list --outdated

# Update a specific package
pip install --upgrade package-name

# Update all packages (careful!)
pip list --outdated --format=json | python -c "import json, sys; print('\n'.join([x['name'] for x in json.load(sys.stdin)]))" | xargs -n1 pip install -U

# Regenerate requirements.txt (if using)
pip freeze > requirements.txt
```

---

## Node.js Setup

### 1. Initialize Project

```bash
# Copy template
cp package.json.template package.json

# Install Node.js 20+ (using nvm)
nvm install 20
nvm use 20

# Verify versions
node --version  # Should be 20+
npm --version   # Should be 10+
```

### 2. Install Dependencies

```bash
# Install all dependencies
npm install

# Verify installation
npm list --depth=0
```

### 3. Security Tools

```bash
# Run npm audit
npm audit

# Fix automatically (careful with breaking changes!)
npm audit fix

# Fix only production dependencies
npm audit fix --only=prod

# Snyk (requires account)
npm install -g snyk
snyk auth
snyk test

# Socket Security
npx socket-npm info <package-name>
```

### 4. Development Tools

```bash
# TypeScript compilation
npm run build

# Watch mode
npm run dev

# Linting
npm run lint

# Auto-fix linting
npm run lint:fix

# Format code
npm run format

# Type checking
npm run typecheck

# Run tests
npm test

# Coverage
npm run test:coverage
```

### 5. Keep Dependencies Updated

```bash
# Check for outdated packages
npm outdated

# Update all dependencies to latest (careful!)
npx npm-check-updates -u
npm install

# Update interactively
npx npm-check-updates -i

# Update specific package
npm update package-name
```

---

## Rust Setup

### 1. Initialize Project

```bash
# Install Rust (if not installed)
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# Copy template
cp Cargo.toml.template Cargo.toml

# Verify Rust version
rustc --version  # Should be 1.75+
cargo --version
```

### 2. Install Dependencies

```bash
# Build project (downloads dependencies automatically)
cargo build

# Build release version
cargo build --release

# Run project
cargo run

# Check without building
cargo check
```

### 3. Security Tools

```bash
# Install cargo-audit
cargo install cargo-audit

# Run security audit
cargo audit

# Install cargo-deny
cargo install cargo-deny

# Initialize cargo-deny config
cargo deny init

# Run cargo-deny
cargo deny check

# Check for updates
cargo install cargo-outdated
cargo outdated
```

### 4. Development Tools

```bash
# Format code
cargo fmt

# Lint (clippy)
cargo clippy

# Fix clippy suggestions
cargo clippy --fix

# Run tests
cargo test

# Run benchmarks
cargo bench

# Generate documentation
cargo doc --open

# Check for unused dependencies
cargo install cargo-udeps
cargo +nightly udeps
```

### 5. Keep Dependencies Updated

```bash
# Update dependencies
cargo update

# Update to latest compatible versions
cargo install cargo-edit
cargo upgrade

# Check for outdated crates
cargo outdated
```

---

## Security Tools Setup

### GitHub Dependabot

```bash
# Already configured in .github/dependabot.yml
# Uncomment the section for your package ecosystem

# Example for Python:
# - package-ecosystem: "pip"

# Enable Dependabot alerts
# Go to: GitHub repo → Settings → Security & analysis → Enable
```

### Supply Chain Security

#### Python: pip-audit
```bash
pip install pip-audit
pip-audit --desc --format json
```

#### Node.js: Socket Security
```bash
# Check a package before installing
npx socket-npm info <package-name>

# Scan your project
npx socket-npm report
```

#### Rust: cargo-audit
```bash
cargo install cargo-audit
cargo audit
```

### Static Analysis Security Testing (SAST)

#### Python: Bandit
```bash
pip install bandit
bandit -r src -f json -o bandit-report.json
```

#### Node.js: ESLint Security Plugin
```bash
npm install --save-dev eslint-plugin-security
# Add to .eslintrc.js: plugins: ['security']
```

#### Rust: Clippy (built-in)
```bash
cargo clippy -- -D warnings
```

---

## CI/CD Configuration

### 1. Enable GitHub Actions

```bash
# Rename security workflow template
mv .github/security-scan.yml.template .github/workflows/security-scan.yml

# Commit and push
git add .github/workflows/security-scan.yml
git commit -m "Add security scanning workflow"
git push
```

### 2. Configure Secrets

For Snyk integration:

1. Go to https://snyk.io and create account
2. Get API token from Account Settings
3. Add to GitHub: Settings → Secrets → New repository secret
4. Name: `SNYK_TOKEN`
5. Value: Your Snyk API token

### 3. Review Security Reports

- Go to: Actions tab → Security Scan workflow
- View artifacts for detailed reports
- Fix critical/high vulnerabilities immediately

---

## Daily Workflow

### Before Starting Work

```bash
# Update dependencies (weekly)
# Python
pip list --outdated

# Node.js
npm outdated

# Rust
cargo outdated

# Pull latest changes
git pull

# Run security scan
# Python: pip-audit
# Node.js: npm audit
# Rust: cargo audit
```

### Before Committing

```bash
# Format code
# Python: black src && ruff check --fix src
# Node.js: npm run format && npm run lint:fix
# Rust: cargo fmt && cargo clippy --fix

# Run tests
# Python: pytest
# Node.js: npm test
# Rust: cargo test

# Type check (if applicable)
# Python: mypy src
# Node.js: npm run typecheck
# Rust: (built into cargo check)
```

### Before Pushing

```bash
# Run full security scan
# Python
pip-audit && bandit -r src

# Node.js
npm audit && npm run lint

# Rust
cargo audit && cargo clippy -- -D warnings

# Ensure all tests pass
# Python: pytest --cov
# Node.js: npm run test:coverage
# Rust: cargo test
```

### Weekly Maintenance

```bash
# Review Dependabot PRs
# - Check changelog for breaking changes
# - Review test results
# - Merge if safe

# Update patch versions
# Python: pip install --upgrade package-name
# Node.js: npm update
# Rust: cargo update

# Clean up unused dependencies
# Python: pip-autoremove (if installed)
# Node.js: npx depcheck
# Rust: cargo +nightly udeps
```

---

## Troubleshooting

### Dependency Conflicts

**Python:**
```bash
# Create fresh virtual environment
rm -rf venv
python -m venv venv
source venv/bin/activate
pip install -e ".[dev]"
```

**Node.js:**
```bash
# Clean install
rm -rf node_modules package-lock.json
npm install
```

**Rust:**
```bash
# Clean build
cargo clean
cargo build
```

### Security Vulnerabilities

1. **Immediate action for CRITICAL/HIGH:**
   - Update to patched version
   - If no patch: find alternative package
   - If no alternative: apply workaround or remove feature

2. **Can defer MEDIUM/LOW:**
   - Plan update in next sprint
   - Monitor for exploitation in the wild

3. **False positives:**
   - Document why it's not a real issue
   - Add to ignore list with justification

### Build Failures

**Python:**
```bash
# Check Python version
python --version

# Upgrade pip
pip install --upgrade pip setuptools wheel

# Install with verbose output
pip install -e ".[dev]" -v
```

**Node.js:**
```bash
# Clear npm cache
npm cache clean --force

# Update npm
npm install -g npm@latest

# Verbose install
npm install --verbose
```

**Rust:**
```bash
# Update rustc
rustup update

# Verbose build
cargo build -vv

# Check specific dependency
cargo tree
```

---

## Additional Resources

### Documentation
- [Python Packaging Guide](https://packaging.python.org/)
- [npm Documentation](https://docs.npmjs.com/)
- [Rust Cargo Book](https://doc.rust-lang.org/cargo/)

### Security
- [OWASP Dependency Check](https://owasp.org/www-project-dependency-check/)
- [Snyk Vulnerability Database](https://security.snyk.io/)
- [RustSec Advisory Database](https://rustsec.org/)
- [npm Security Best Practices](https://docs.npmjs.com/about-security-advisories)

### Tools
- [pip-audit](https://pypi.org/project/pip-audit/)
- [Safety](https://pypi.org/project/safety/)
- [Snyk CLI](https://docs.snyk.io/snyk-cli)
- [Socket Security](https://socket.dev/)
- [cargo-audit](https://crates.io/crates/cargo-audit)

---

*Last updated: 2025-12-19*
