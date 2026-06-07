# Making Your First Contribution to Dipralix

This guide is for you if you're:
- New to contributing to open source
- Curious about Dipralix's codebase
- Want to make a real impact with your first PR

Read this, pick a task, and ship it.

---

## 🎯 Why Contribute?

- **You own the code you read.** Every contributor gets commit history and maintainer acknowledgment.
- **Real-world Rust.** Dipralix is production code — async, multi-model, encryption, P2P protocols.
- **Fast feedback.** We read all PRs within 24 hours. We value small PRs over big ones.
- **Small increments.** Most contribution opportunities take 30 minutes to 2 hours.

---

## 🚀 Quick Start

### 1. Set Up Your Environment

```bash
git clone https://github.com/Zyferon/dipralix
cd dipralix
cargo build --release
./target/release/dipralix-cli --version
```

**Troubleshooting:**
- Need Rust? Install from https://rustup.rs/ (1.75+)
- On macOS? Might need Xcode: `xcode-select --install`
- On Linux? Install build tools: `sudo apt install build-essential`

### 2. Watch the Dev Loop

```bash
# In one terminal
cargo watch -x check -x clippy -x test
```

This rebuilds on every file change. Instant feedback.

### 3. Read the Code Map

Open [`CONTRIBUTING.md`](CONTRIBUTING.md#where-things-live) and find the module you're interested in. Here are the friendly ones:

- `src/ui.rs` — terminal UI strings, colors, formatting
- `src/browser.rs` — simple HTTP fetcher + HTML→Markdown
- `src/fingerprint.rs` — project detection + quality scoring
- `src/comment_protocol.rs` — scan for `// DIPRALIX:` directives
- `tests/` — integration tests (great to read!)

---

## 🎓 Task Types & Difficulty

### 🟢 Green: 30 mins (Best for first-timers)

#### **G1: Add a new `/help` topic** ⭐⭐☆

**What:** Dipralix has many slash commands. Add one more help section.

**Where:** `src/ui.rs::print_help()`

**How:**
1. Find the `print_help()` function
2. Look at how other commands are documented
3. Add a new line for your command (e.g., `/purge` for cache clearing)
4. One sentence description

**Example:**
```rust
// Near line ~150 in print_help()
println!("  /purge          clear the local web cache");
```

**Check:** Run `dipralix-cli` and type `/help` — your command appears.

**Time:** 15 minutes

---

#### **G2: Add a test case for an existing tool** ⭐⭐☆

**What:** Write a test that verifies a tool works (e.g., `read_file` can handle symlinks).

**Where:** `tests/integration_tests.rs` or `src/tools.rs::tests`

**How:**
1. Read an existing test in `tests/integration_tests.rs`
2. Copy the structure
3. Add a test case for an edge case (symlink, permission, encoding)

**Example:**
```rust
#[test]
fn test_read_file_with_utf8() {
    // Create a test file with UTF-8 content
    // Call read_file
    // Assert the content matches
}
```

**Check:** `cargo test` passes

**Time:** 20 minutes

---

#### **G3: Improve error messages** ⭐⭐☆

**What:** Find an error message and make it clearer.

**Where:** Anywhere in `src/` where we `return Err(...)`

**How:**
1. Reproduce an error (e.g., missing API key, invalid config)
2. Read the message — is it clear?
3. Improve it with context or actionable next steps

**Example (before):**
```
Error: missing key
```

**Example (after):**
```
Error: DIPRALIX_API_KEY not set. Get a free key at https://aistudio.google.com/apikey
```

**Check:** Run the error scenario again, verify message is clearer

**Time:** 15 minutes

---

### 🟡 Yellow: 1–2 hours (Intermediate)

#### **Y1: Add a new integration test** ⭐⭐⭐

**What:** Test the full agent loop (prompt → planning → execution → verification).

**Where:** `tests/integration_tests.rs`

**How:**
1. Read the existing test harness (look for `setup_test_env()`)
2. Write a test that exercises the agent loop
3. Mock the provider (Gemini/Claude) response
4. Assert the final state

**Example:**
```rust
#[tokio::test]
async fn test_agent_loop_with_file_read() {
    let env = setup_test_env().await;
    let result = env.agent.prompt("read src/main.rs").await;
    assert!(result.contains("fn main"));
}
```

**Time:** 90 minutes

---

#### **Y2: Add support for a new file type in static analysis** ⭐⭐⭐

**What:** Extend `/infra security` to check a new file type (e.g., `docker-compose.yml`, `Makefile`).

**Where:** `src/infra.rs`

**How:**
1. Read how Dockerfile checks work
2. Add a new check function (e.g., `scan_docker_compose()`)
3. Call it from the main `scan()` function
4. Add a test

**Example:**
```rust
fn scan_docker_compose(path: &Path) -> Vec<Issue> {
    // Parse YAML, check for common issues
    // Return issues
}
```

**Time:** 90 minutes

---

#### **Y3: Add a new memory summarization strategy** ⭐⭐⭐

**What:** Improve how Dipralix compresses old memory (today it's simple concatenation).

**Where:** `src/memory.rs::compact()`

**How:**
1. Read the current logic in `memory.rs`
2. Add a new strategy (e.g., cluster similar decisions, rank by recency)
3. Test it on a synthetic memory file

**Time:** 2 hours

---

### 🔴 Red: 2–4 hours (Advanced)

#### **R1: Add support for a new integration (e.g., Jira)** ⭐⭐⭐⭐

**What:** Let Dipralix read/create Jira issues alongside GitHub and Discord.

**Where:** `src/integrations/`

**How:**
1. Create `src/integrations/jira.rs`
2. Implement the `IntegrationService` trait
3. Register it in `IntegrationRegistry`
4. Add config to `src/config.rs`
5. Test with a real Jira instance (or mock the API)

**Time:** 3 hours

---

#### **R2: Implement CRDT merge for file sync** ⭐⭐⭐⭐

**What:** Replace last-write-wins with character-level CRDT merging in the P2P mesh.

**Where:** `src/sync/merge.rs` (new file)

**How:**
1. Study the Conflict-free Replicated Data Type (CRDT) algorithm
2. Implement character-level merging (research: Yjs or Automerge)
3. Test convergence on two simulated peers
4. Integrate into `src/sync/protocol.rs`

**Time:** 4 hours (or more if new to CRDTs)

---

## 📋 How to Submit

### 1. Find Your Task

Go to [GitHub Issues](https://github.com/Zyferon/dipralix/issues) and look for:
- `good first issue` — we hand-picked these
- `help wanted` — we'd love your contribution
- `roadmap` — features on the horizon
- Any issue you feel ready for

### 2. Claim It

Comment on the issue:
```
I'd like to work on this. I'll submit a PR by [DATE].
```

We'll mark it `assigned` and hold it for you (48 hours).

### 3. Create a Branch

```bash
git checkout -b fix/your-feature-name
```

Follow the naming:
- `fix/` — bug fix
- `feat/` — new feature
- `docs/` — documentation
- `refactor/` — code cleanup

### 4. Make Your Changes

```bash
# Make edits
cargo check
cargo clippy -- -D warnings
cargo test
```

All three must pass.

### 5. Commit

```bash
git add .
git commit -m "fix: descriptive message (no emoji)"
```

**Commit message format:**
```
type: short description (50 chars max)

Optional longer explanation. Reference issues:
Fixes #123
```

Types: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`

### 6. Push & Open a PR

```bash
git push origin fix/your-feature-name
```

Then open a PR on GitHub. Use the template. Include:
- **What:** What does this change?
- **Why:** Why does it matter?
- **Testing:** How did you verify it works?

### 7. We Review

We'll read it within 24 hours. If we ask for changes:
```bash
# Make edits
git add .
git commit -m "fix: address review feedback"
git push
```

GitHub auto-updates the PR. No need to open a new one.

---

## ✅ Checklist Before You Submit

- [ ] `cargo check` passes (no compilation errors)
- [ ] `cargo clippy -- -D warnings` passes (no warnings)
- [ ] `cargo test` passes (all tests)
- [ ] `cargo fmt -- --check` passes (code style)
- [ ] No TODO/FIXME/unimplemented!() in your diff
- [ ] No emoji in code (we're emoji-free by design)
- [ ] Commit message follows format (`type: description`)
- [ ] If user-facing, added line to `CHANGELOG.md` (unreleased section)

---

## 🆘 Getting Stuck?

**Compiler error?**
- Read the error carefully (Rust errors are verbose but helpful)
- Google the error type
- Ask in [GitHub Discussions](https://github.com/Zyferon/dipralix/discussions)

**Don't understand the code?**
- Read the adjacent code (context is key)
- Check the `CONTRIBUTING.md` module map
- Open a discussion: "I'm trying to understand X, can you explain?"

**Need a second opinion?**
- Comment on your draft PR: "WIP — would appreciate feedback on approach"
- We'll review early and steer you

---

## 🎉 After Your PR Ships

- Your name goes in [`CHANGELOG.md`](CHANGELOG.md) (under Contributors)
- You're credited in the release notes
- You can add yourself to a [CONTRIBUTORS](CONTRIBUTORS.md) file (if you want)

---

## Examples of Shipped First Contributions

_(Coming soon — be the first!)_

---

## More Questions?

- [**GitHub Discussions**](https://github.com/Zyferon/dipralix/discussions) — Ask anything
- [**GitHub Issues**](https://github.com/Zyferon/dipralix/issues) — Report bugs, request features
- [**CONTRIBUTING.md**](CONTRIBUTING.md) — Full contribution guide

Welcome to the Dipralix community. We're excited to see what you build.
