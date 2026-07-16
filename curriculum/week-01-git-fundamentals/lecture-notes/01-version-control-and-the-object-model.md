# Lecture 1 — Version control and Git's object model

> **Duration:** ~2 hours. **Outcome:** You can explain what version control is without hand-waving, name Git's four object types, and describe how content-addressing turns a file's *contents* into its *name* — so you know why two identical files are stored only once.

## 1. What version control actually is

A **version control system** (VCS) answers three questions that every project eventually asks:

1. **What changed?** — between now and any point in the past.
2. **Who changed it, and why?** — attribution and intent.
3. **Can I go back?** — safely, to any previous state, without losing the present.

Before VCS, people solved this with folders named `project-final`, `project-final-v2`, `project-really-final`. That "works" for one person for one afternoon and collapses the moment two people edit the same file, or you need last Tuesday's version of one file but today's version of another.

A VCS replaces the folder-copy habit with a **history you can query**. Git is a **distributed** VCS: every clone contains the *entire* history, not just the latest snapshot. There is no privileged central copy at the level of the model — GitHub is a convention, not a requirement.

| Approach | "What changed?" | Two editors at once | Go back in time |
|----------|-----------------|---------------------|-----------------|
| Copy folders | Manual diff of two folders | Overwrite each other | Dig through named copies |
| Centralized VCS (SVN, CVS) | Ask the server | Server mediates locks/merges | Server round-trip |
| Git (distributed) | Local, instant | Merge histories | Local, instant |

### Snapshots, not deltas

Here is the single idea that makes Git different from most systems people have used before. Many older tools store a file as *the original plus a list of changes* (deltas): "line 4 changed, line 9 deleted." Git does not think that way. **Git stores snapshots.** Each commit records the complete state of every tracked file at that moment.

"But that must waste enormous space!" It doesn't, and the reason is the whole subject of this lecture: Git names things by their content, so an unchanged file across a hundred commits is stored exactly **once** and pointed at a hundred times.

## 2. Content-addressing: the one big idea

Normally, *you* pick a name (a filename) and the system stores whatever you put under it. **Content-addressed storage inverts this.** You hand Git some bytes; Git computes a cryptographic hash of those bytes and uses *that hash* as the name. The name is derived from the content, not chosen.

Git's hash is (historically) **SHA-1**: a function that turns any input into a fixed 160-bit fingerprint, written as 40 hexadecimal characters.

```text
"hello\n"  ──SHA-1──▶  ce013625030ba8dba906f756967f9e9ca394464a
```

Three consequences fall out of this design, and they explain almost everything about Git:

- **Immutability.** Change one byte and the hash changes completely. An object can never be silently altered — a different content is, by definition, a different object with a different name.
- **Deduplication.** Identical content produces an identical hash, so it is stored once. Copy a file into ten directories; Git stores one blob.
- **Integrity.** To verify nothing is corrupted, Git re-hashes the content and checks it matches the name. Corruption is detectable, not silent.

> **SHA-1 vs SHA-256.** SHA-1 has known collision weaknesses for *adversarial* inputs. Git added a SHA-256 object format; you can opt in with `git init --object-format=sha256`. Most repositories today (including GitHub) are still SHA-1, and Git deployed extra collision-detection to mitigate the risk. The *model* is identical either way — only the hash length changes (40 hex chars for SHA-1, 64 for SHA-256). This course uses SHA-1 in examples; everything transfers.

You can compute an object hash yourself, without even making a commit:

```bash
printf 'hello\n' | git hash-object --stdin
# ce013625030ba8dba906f756967f9e9ca394464a
```

That number is not random and not a counter. It is a fact about the bytes `hello\n`. Run it on any machine, any year — same answer.

## 3. The four object types

Everything Git stores is one of exactly **four** object types. All four live in the same content-addressed database (`.git/objects/`), and all four are named by the SHA of their content.

| Object | Stores | Points at | Analogy |
|--------|--------|-----------|---------|
| **blob** | The raw contents of a file (no name, no metadata) | Nothing | A file's *bytes* |
| **tree** | A directory listing: names + modes + SHAs | Blobs and other trees | A *folder* |
| **commit** | A snapshot pointer + metadata | One tree + parent commit(s) | A *save point* |
| **tag** | An annotated, signable label | Usually one commit | A *named bookmark* |

Read that table twice. The relationships are the entire model:

```text
commit ──▶ tree ──▶ blob   (file contents)
   │         └────▶ tree ──▶ blob
   ▼
 parent commit ──▶ tree ──▶ ...
```

### Blobs — the bytes of a file

A **blob** is just file content. It has no filename, no path, no timestamp — only bytes. The filename lives in the *tree* that references the blob, not in the blob itself. That is why renaming a file changes a tree but not the blob: the content didn't change, so its hash didn't change.

### Trees — directories

A **tree** is a directory. It is a list of entries, each with: a **mode** (e.g. `100644` for a normal file, `100755` for executable, `040000` for a subdirectory), a **type** (`blob` or `tree`), a **SHA**, and a **name**.

```text
100644 blob a1b2c3…  README.md
100644 blob d4e5f6…  main.py
040000 tree 789abc…  src
```

A tree can point at other trees — that is how nested directories are represented. The top-level tree of a commit is often called the **root tree**.

### Commits — snapshots with a story

A **commit** wraps a single **root tree** (the full snapshot of your project at that moment) together with:

- the SHA of that tree,
- zero or more **parent** commit SHAs (zero for the first commit; two or more for a merge),
- **author** name/email/time and **committer** name/email/time,
- the **commit message**.

Because a commit records its parent(s), the commits form a **directed graph** pointing backward in time. That graph *is* your history. Follow the parent links and you can reconstruct every past state.

Crucially, a commit does **not** store a diff. It stores a pointer to a complete tree. Git *computes* diffs on demand by comparing two trees — it never stores them.

### Tags — human-friendly labels

An **annotated tag** is an object that names a commit and adds a message, tagger, and optional GPG signature — typically used for releases (`v1.0.0`). (There are also *lightweight* tags, which are just a ref pointing straight at a commit with no object of their own. More on refs in Lecture 3.)

## 4. Watch an object get born

Let's make the model concrete. `hash-object -w` writes a blob into the database and prints its name:

```bash
mkdir /tmp/git-demo && cd /tmp/git-demo
git init -q
echo 'hello' > greeting.txt
git hash-object -w greeting.txt
# ce013625030ba8dba906f756967f9e9ca394464a
```

That blob now exists in `.git/objects/`, even though we never ran `git add` or `git commit`. Ask Git what it is:

```bash
git cat-file -t ce01362   # type  →  blob
git cat-file -s ce01362   # size  →  6
git cat-file -p ce01362   # print →  hello
```

Short SHAs work as long as they're unambiguous — Git needs only enough characters to identify one object.

Now notice deduplication directly. Make a second file with the *same content*:

```bash
echo 'hello' > copy.txt
git hash-object copy.txt
# ce013625030ba8dba906f756967f9e9ca394464a   ← identical SHA
```

Same bytes, same name, same single object. Two files, one blob.

## 5. How a commit is assembled

When you run `git commit`, Git does something you could do by hand (and will, in this week's mini-project):

1. Takes everything currently staged and builds a **tree** object for it (recursively, one tree per directory). This is what `git write-tree` does.
2. Creates a **commit** object pointing at that root tree, at the current commit as parent, with your message and identity. This is `git commit-tree`.
3. Moves the current branch ref to point at the new commit.

You can literally perform those steps with plumbing:

```bash
git add greeting.txt
tree=$(git write-tree)                       # build a tree from the index
commit=$(echo "first commit" | git commit-tree "$tree")
git update-ref refs/heads/main "$commit"     # point the branch at it
git log --oneline                            # there's your commit
```

If that feels like pulling back the curtain — good. `git commit` is a convenience wrapper around exactly this. Understanding the wrapper is understanding Git.

## 6. Why the model makes Git fast and safe

Once you see the object graph, Git's reputation stops being mysterious:

- **Branches are cheap** because a branch is just a 41-byte file holding one commit SHA (Lecture 3). Creating a branch creates no objects at all.
- **History is trustworthy** because a commit's SHA depends on its tree *and* its parent SHA. Alter anything in the past and every descendant SHA changes — tampering is loud, not silent.
- **Diffs are always available** because Git can compare any two trees, even across years, without having stored a single diff.
- **Nothing is truly lost** the moment it's committed: the object stays in the database even if no branch points at it, which is exactly what makes `reflog` recovery (Lecture 3, Challenge 2) possible.

## 7. Check yourself

- In one sentence: what is version control *for*?
- Does Git store diffs between commits, or snapshots? What is the practical consequence for storage?
- Name the four object types and what each one points at.
- Where does a file's *name* live — in the blob or in the tree? Why does that mean a rename doesn't create a new blob?
- What is content-addressing, and what three properties does it give Git?
- Two files contain the exact same bytes. How many blobs does Git store? Why?
- Roughly, what three things does `git commit` do to the object database and refs?

If you can answer all seven from memory, continue to Lecture 2.

## Further reading

- **Pro Git — "Git Internals: Git Objects"** (free, the canonical reference): <https://git-scm.com/book/en/v2/Git-Internals-Git-Objects>
- **Pro Git — "Getting Started: About Version Control":** <https://git-scm.com/book/en/v2/Getting-Started-About-Version-Control>
- **"Git from the inside out" — Mary Rose Cook:** <https://maryrosecook.com/blog/post/git-from-the-inside-out>
