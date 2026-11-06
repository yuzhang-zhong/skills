# Skills in version control

[Back to the README](../README.md)

For a shared project, commit `skills-lock.json` and restore installed skills after
cloning. Keep the source of any skills your team writes in version control too.
Installed copies and agent links can be ignored rather than committed.

## Ignore installed files

Add patterns for the skill directories your project uses to its root `.gitignore`:

```gitignore
# Installed skills and agent links
/.agents/skills/*
/.claude/skills/*
/.cursor/skills/*
```

These are examples for the shared directory, Claude Code, and Cursor. Adjust the
agent paths using the [supported agents reference](agents.md). Ignore only the
skill directories, not the agents' whole configuration directories.

`skills add` does not edit `.gitignore`. Adding these patterns also does not stop
Git from tracking files already committed; review any existing tracked copies
before changing how your project manages them.

## Keep team-authored skills

If a skill lives directly in `.agents/skills/`, add an exception after the ignore
pattern for each skill your team maintains:

```gitignore
/.agents/skills/*
!/.agents/skills/review-workflow/
```

This keeps `review-workflow/SKILL.md` and its supporting files available to commit,
while ignoring installed sibling skills. Do not ignore the entire `.agents/` or
`.agents/skills/` directory: Git cannot re-include a child of an ignored directory.
Replace any such broader pattern before using this exception. Apply the same
approach if you keep authored skills in an agent-specific directory.

Alternatively, keep the source outside the install directories, for example at
`skills/review-workflow/SKILL.md`, and install it from that local path:

```bash
npx skills add ./skills/review-workflow --agent claude-code --yes
```

Commit the `skills/review-workflow/` source directory and `skills-lock.json`, but
ignore the installed copies and links. Local sources inside the project are
recorded relative to the project, so they can be restored from a fresh clone.
A local source outside the repository must also be available on each machine;
the lock file does not include its contents.

## Restore after cloning

Run this from the project root:

```bash
npx skills install
```

Restore does not prompt. Use `--agent` to choose the agent directories explicitly:

```bash
npx skills install --agent claude-code
```

Install npm dependencies first if the lock includes skills provided by those
packages. Restore does not install npm packages. See the
[restore reference](commands.md#restore-skills-from-the-project-lock-file) for
agent selection and other options.

To require restored content to match the lock without updating the lock file:

```bash
npx skills install --frozen-lockfile --agent claude-code
```

Frozen mode is the default in CI. The lock is an install record, not an archive:
sources must still be accessible, and changed content can make a frozen restore
fail. [Pin a Git tag or commit when adding a skill](commands.md#source-formats)
when you need a stable remote source. Restore normally leaves an already installed
skill in place; use `--force` to fetch it again.

## Review updates together

After adding, updating, or removing skills, review and commit the corresponding
`skills-lock.json` change. When you edit a local source skill, reinstall it from
that source and commit the source and lock changes together.

Committing installed skill directories is also supported. If your project uses
that approach, keep the installed files and lock file in sync in the same commit
instead of adding ignore patterns for those files.
