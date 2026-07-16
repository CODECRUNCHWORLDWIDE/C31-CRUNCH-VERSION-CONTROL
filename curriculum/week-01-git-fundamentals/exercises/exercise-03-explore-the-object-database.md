# Exercise 3 — Explore the object database

**Goal:** Use only plumbing commands to descend from a commit to its file bytes, and to *create* a blob by hand. By the end, `.git/objects/` is a place you can read, not a black box.

**Estimated time:** 50 minutes. **Do it after:** Lecture 3.

## Setup

```bash
mkdir -p ~/c31-w1/exercise-03
cd ~/c31-w1/exercise-03
git init -q
mkdir docs
echo 'print("hi")'   > app.py
echo '# Guide'        > docs/guide.md
git add .
git commit -q -m "initial"
```

## Part A — Create a blob without committing

Prove that content-addressing is deterministic and that blobs exist independent of commits:

```bash
printf 'hello\n' | git hash-object --stdin      # compute the SHA, store nothing
printf 'hello\n' | git hash-object -w --stdin    # store it, print the SHA
git cat-file -t <that-sha>                       # blob
git cat-file -p <that-sha>                       # hello
```

Record in `walk.md`:

1. The SHA for `hello\n`. Compare it with a classmate or a second machine — is it the same? Why?

## Part B — Walk commit → tree → tree → blob

Descend the object graph one hop at a time. **Copy the real SHAs from your own output** as you go.

```bash
git rev-parse HEAD                 # (1) the commit SHA
git cat-file -p HEAD               # read it — note the "tree ..." line
git cat-file -p HEAD^{tree}        # (2) the root tree: lists app.py (blob) + docs (tree)
git ls-tree HEAD docs              # (3) the docs subtree: lists guide.md (blob)
git cat-file -p <guide.md-blob>    # (4) the bytes: "# Guide"
```

In `walk.md`, write the four SHAs and label each with its type:

2. commit SHA = …
3. root tree SHA = …
4. `docs` tree SHA = …
5. `docs/guide.md` blob SHA = …

Then confirm the chain end-to-end in one line and paste the result:

```bash
git cat-file -p "$(git rev-parse HEAD:docs/guide.md)"    # should print "# Guide"
```

## Part C — Prove deduplication

```bash
cp app.py app-copy.py              # same bytes as app.py
git add app-copy.py
git ls-files --stage               # look at the blob SHAs for app.py and app-copy.py
```

6. Are the two blob SHAs identical? Explain in one sentence why Git stores the content only once.

## Part D — Object types round-trip

```bash
git count-objects -v               # how many loose objects exist
git cat-file --batch-check --batch-all-objects | sort -k2   # list every object + type
```

7. From that listing, how many blobs, trees, and commits does your repo currently have? Do the numbers make sense given what you committed?

## Expected result

- A `walk.md` recording the full `commit → tree → tree → blob` path with real SHAs.
- You created a blob with `hash-object -w` and read it back.
- You demonstrated deduplication with two identical files.

## Done when…

- [ ] `walk.md` lists all four SHAs down the object graph, correctly typed.
- [ ] The one-liner `git cat-file -p "$(git rev-parse HEAD:docs/guide.md)"` printed the file.
- [ ] You confirmed `app.py` and `app-copy.py` share one blob SHA.
- [ ] You counted the objects and the total matches your mental model.

## Stretch

- Find where your commit object physically lives on disk: `f=$(git rev-parse HEAD); echo .git/objects/${f:0:2}/${f:2}`. Confirm the file exists. Try to `cat` it — why is it unreadable? (Hint: zlib + a header.)
- Build a tree and a commit *by hand* with `git write-tree` and `git commit-tree`, then point a new ref at it with `git update-ref`. Verify with `git log`.

## Submission

Commit `walk.md` and save it to your portfolio under `c31-week-01/exercise-03/`.
