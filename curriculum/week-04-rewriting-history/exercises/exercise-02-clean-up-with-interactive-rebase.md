# Exercise 2 — Clean Up a Branch with Interactive Rebase

**Goal:** Take a feature branch of six messy commits — wip, typos, a debug commit, a badly-named one — and rewrite it into three clean, logical commits ready for review. This is the exact skill you'll use before every real pull request.

**Estimated time:** 50 minutes.

## Setup

Set your editor first so `rebase -i` doesn't ambush you:

```bash
git config --global core.editor "nano"     # or code --wait / vim
```

Build the messy branch:

```bash
mkdir -p /tmp/c31-w4/ex2 && cd /tmp/c31-w4/ex2
git init -q
echo "# Calculator" > README.md && git add . && git commit -qm "chore: init"

git switch -c feature
echo "def add(a,b): return a+b"      > calc.py && git add . && git commit -qm "feat: add addition"
echo "print('debug')"               >> calc.py && git add . && git commit -qm "debug print"
echo "def sub(a,b): return a-b"     >> calc.py && git add . && git commit -qm "wip"
echo "# fixed a typo"               >> calc.py && git add . && git commit -qm "fix typo"
echo "## Usage"                     >> README.md && git add . && git commit -qm "docs: usage"
echo "### Examples"                 >> README.md && git add . && git commit -qm "more docs"

git log --oneline                    # 6 commits on feature, plus init
```

## The target

You want the reviewer to see exactly **three** commits on `feature`:

```
feat: add addition and subtraction
docs: document usage and examples
```

Wait — that's two. The third is the `chore: init` on the base, which you leave alone. So your rebase turns the six `feature` commits into **two** clean ones, and drops the debug commit entirely.

Concretely you must:

1. **Squash** `wip` (the subtraction commit) into `feat: add addition` — and reword the result to `feat: add addition and subtraction`.
2. **Drop** the `debug print` commit completely.
3. **Fixup** `fix typo` into the addition/subtraction feature (its message is junk).
4. **Squash** `more docs` into `docs: usage` and reword to `docs: document usage and examples`.

## Steps

1. Start the interactive rebase over all six feature commits:

   ```bash
   git rebase -i HEAD~6
   ```

2. In the editor you'll see the six commits, oldest at the top. Rearrange and relabel them. A working to-do list looks like this (remember: `squash`/`fixup` merge into the line **above**, and you may reorder lines):

   ```
   reword feat: add addition
   pick   wip                    # subtraction — becomes squash below after reorder
   ```

   The cleanest arrangement:

   ```
   reword <sha> feat: add addition
   squash <sha> wip
   fixup  <sha> fix typo
   drop   <sha> debug print
   reword <sha> docs: usage
   squash <sha> more docs
   ```

   (Order the lines so each `squash`/`fixup` sits directly beneath the commit it should join. Move the `docs` lines below the feature lines.)

3. Save and close. Git will:
   - Stop to let you **reword** `feat: add addition` → type `feat: add addition and subtraction`.
   - Combine the squashed commits (it opens an editor with both messages — keep the good one).
   - **Reword** the docs commit → `docs: document usage and examples`.

4. If a conflict appears (both feature commits edit `calc.py`), resolve the file, then:

   ```bash
   git add calc.py
   git rebase --continue
   ```

5. Verify:

   ```bash
   git log --oneline
   # <sha> docs: document usage and examples
   # <sha> feat: add addition and subtraction
   # <sha> chore: init
   ```

## Expected result

Exactly three commits total (`chore: init` + your two clean ones). The `debug print` commit is gone. No `wip`, `fix typo`, or `more docs` messages survive. Every actual code and docs change is still present in the files:

```bash
cat calc.py       # has add() and sub(); the debug line is gone
cat README.md     # has Usage and Examples sections
```

## Done when…

- [ ] `git log --oneline` shows exactly three commits with clean, conventional messages.
- [ ] The `debug print` commit and its `print('debug')` line are both gone.
- [ ] `calc.py` still contains both `add` and `sub`; `README.md` still has both doc sections.
- [ ] You can explain why `fixup` was right for `fix typo` but `squash` was right for `more docs`.
- [ ] A screenshot or paste of the final `git log --oneline` is committed under `c31-week-04/exercise-02/`.

## If it goes sideways

Abort and start over — no harm done:

```bash
git rebase --abort
```

## Stretch

- Redo the exercise using the `--autosquash` workflow instead: after building the messy branch, mark the junk commits with `git commit --fixup <sha>` as you go, then run `git rebase -i --autosquash HEAD~6` and watch Git pre-build the to-do list.
- Use `edit` on the addition commit to **split** it into two commits (one per function) using `git reset HEAD~1`.
