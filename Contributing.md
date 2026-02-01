# Contributing to KnexCoin

Thank you for your interest in contributing to KnexCoin! This guide will help you get started.

---

## Ways to Contribute

### Code Contributions
- Bug fixes
- New features
- Performance improvements
- Documentation

### Non-Code Contributions
- Bug reports
- Feature suggestions
- Documentation improvements
- Community support
- Translations

---

## Getting Started

### 1. Fork the Repository

```bash
# Fork on GitHub, then clone
git clone https://github.com/YOUR_USERNAME/knexcoin.git
cd knexcoin
```

### 2. Set Up Development Environment

```bash
# Rust node
cd "Knex Project/node"
cargo build

# Python backend
cd ../../KnexDAG
pip install -r requirements.txt
pip install -r requirements-dev.txt

# Frontend
cd ../idex-svelte
npm install
```

### 3. Create a Branch

```bash
git checkout -b feature/your-feature-name
# or
git checkout -b fix/bug-description
```

---

## Development Workflow

### Running Tests

```bash
# Rust tests
cd "Knex Project/node"
cargo test

# Python tests
cd KnexDAG
pytest

# Frontend tests
cd idex-svelte
npm test
```

### Code Style

#### Rust
- Follow [Rust style guidelines](https://doc.rust-lang.org/style-guide/)
- Run `cargo fmt` before committing
- Run `cargo clippy` for linting

```bash
cargo fmt
cargo clippy -- -D warnings
```

#### Python
- Follow PEP 8
- Use type hints
- Run `black` and `flake8`

```bash
black src/
flake8 src/
mypy src/
```

#### TypeScript/JavaScript
- Use TypeScript where possible
- Follow ESLint configuration
- Run Prettier

```bash
npm run lint
npm run format
```

---

## Commit Guidelines

### Commit Message Format

```
type(scope): brief description

Longer description if needed.

Fixes #123
```

### Types

| Type | Description |
|------|-------------|
| `feat` | New feature |
| `fix` | Bug fix |
| `docs` | Documentation |
| `style` | Formatting (no code change) |
| `refactor` | Code restructuring |
| `perf` | Performance improvement |
| `test` | Adding tests |
| `chore` | Maintenance tasks |

### Examples

```
feat(validator): add bandwidth proof verification

fix(api): handle missing account gracefully

docs(wiki): update API reference

refactor(ledger): simplify block validation logic
```

---

## Pull Request Process

### 1. Before Submitting

- [ ] Tests pass locally
- [ ] Code formatted properly
- [ ] Documentation updated (if needed)
- [ ] Commit messages follow guidelines
- [ ] Branch is up to date with main

### 2. PR Description Template

```markdown
## Description
Brief description of changes.

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing
Describe how you tested your changes.

## Checklist
- [ ] Tests pass
- [ ] Code follows style guidelines
- [ ] Self-reviewed code
- [ ] Documentation updated
```

### 3. Review Process

1. Submit PR to `main` branch
2. Automated tests run
3. Maintainer reviews
4. Address feedback
5. Merge when approved

---

## Project Structure

```
knexcoin/
├── Knex Project/
│   └── node/              # Rust full node
│       └── src/
│           ├── ledger.rs      # Transaction processing
│           ├── validation_tiers.rs  # SPVT
│           ├── emission.rs    # Token emission
│           └── ...
├── KnexDAG/               # Python backend
│   └── src/
│       ├── api.py         # REST API
│       ├── dag.py         # DAG operations
│       └── ...
├── idex-svelte/           # AyeDEX frontend
│   └── src/
│       ├── lib/           # Stores and utilities
│       └── routes/        # Pages
├── Knexpay/               # Marketing site
├── sections/              # Whitepaper
└── wiki/                  # Documentation
```

---

## Component Guidelines

### Rust Node

**Location:** `Knex Project/node/src/`

Key principles:
- Use `Result<T, Error>` for fallible operations
- Implement proper error types
- Add comprehensive unit tests
- Document public APIs

```rust
/// Validates a block against network rules.
///
/// # Arguments
/// * `block` - The block to validate
///
/// # Returns
/// * `Ok(())` if valid
/// * `Err(ValidationError)` if invalid
pub fn validate_block(block: &Block) -> Result<(), ValidationError> {
    // Implementation
}
```

### Python Backend

**Location:** `KnexDAG/src/`

Key principles:
- Use type hints
- Handle exceptions gracefully
- Write docstrings
- Keep functions focused

```python
def get_balance(address: str) -> dict:
    """
    Get account balance and pending amounts.

    Args:
        address: KnexCoin address (knex_...)

    Returns:
        Dict with balance, pending, and block_count

    Raises:
        AccountNotFoundError: If account doesn't exist
    """
    # Implementation
```

### Frontend (Svelte)

**Location:** `idex-svelte/src/`

Key principles:
- Use TypeScript
- Keep components small
- Use stores for state
- Follow accessibility guidelines

```svelte
<script lang="ts">
  import { wallet } from '$lib/stores/wallet';

  export let amount: number;

  async function handleSend() {
    // Implementation
  }
</script>

<button on:click={handleSend} aria-label="Send KNEX">
  Send {amount} KNEX
</button>
```

---

## Testing Guidelines

### Unit Tests

Test individual functions in isolation:

```rust
#[test]
fn test_block_validation() {
    let block = create_test_block();
    assert!(validate_block(&block).is_ok());
}
```

### Integration Tests

Test components working together:

```python
def test_send_and_receive_flow():
    # Create accounts
    alice = create_account()
    bob = create_account()

    # Fund Alice via faucet
    faucet_response = api.faucet(alice.address)
    assert faucet_response.success

    # Alice sends to Bob
    send_response = api.send(alice, bob.address, 100)
    assert send_response.success

    # Bob receives
    receive_response = api.receive(bob, send_response.hash)
    assert receive_response.success
```

### Coverage

Aim for:
- 80%+ coverage on critical paths
- 100% coverage on cryptographic code
- Test edge cases and error conditions

---

## Security

### Reporting Vulnerabilities

**DO NOT** open public issues for security vulnerabilities.

Email: security@knexcoin.io

Include:
- Description of vulnerability
- Steps to reproduce
- Potential impact
- Suggested fix (if any)

### Security Review Checklist

- [ ] No hardcoded secrets
- [ ] Input validation
- [ ] Proper error handling (no info leaks)
- [ ] Cryptographic operations reviewed
- [ ] No SQL injection (if applicable)
- [ ] Rate limiting considered

---

## Documentation

### Wiki Pages

Wiki pages use GitHub-flavored Markdown:
- Use clear headings
- Include code examples
- Add diagrams where helpful
- Link to related pages

### Code Documentation

- Document public APIs
- Explain complex algorithms
- Include usage examples
- Keep docs up to date

---

## Community

### Communication Channels

- **GitHub Issues** - Bug reports, feature requests
- **GitHub Discussions** - General questions
- **Discord** - Real-time chat (coming soon)

### Code of Conduct

- Be respectful and inclusive
- Provide constructive feedback
- Help newcomers
- Focus on the technical merits

---

## Recognition

Contributors are recognized in:
- Release notes
- Contributors list
- Community highlights

---

## Questions?

- Check existing issues/discussions
- Read the documentation
- Ask in the community channels

Thank you for contributing to KnexCoin!
