# Introduction
This is a git repository that is used for educational
purposes in order to learn key git concepts and
typical work flows.

# History
1. Created initial commit.
2. Added introduction to README.md.
3. Developer A adds the history information along with this line.
4. Developer B adds information about common git actions, starting with git log.
Developer B started before Developer A delivered their commit so Developer B
**merged** origin/main into their local main branch to get the latest changes.
This line was added in the merge commit in order to align with changes to the
code that was done by someone else. Developer A introduced the concept of the
history section, so now Developer B needs to fix their commit to align with how
things work now.

# Common actions

## Inspect changes to branches and commits
There are many ways to see changes in git, this section will explain different
ways of investigating the state of branches and how to identify what has changed.

### See the commits that exist on the currently checked out branch
```
> git log

commit 13430ab22e3354ca168a1213c529f1a1e464af72 (HEAD -> main)
Author: Max Ringström <max.ringstrom@tetrapak.com>
Date:   Tue Jun 25 08:16:37 2024 +0200

    Add introduction to README.md

commit c7921eb9f5b29d313b081fb425d24634787be394
Author: Max Ringström <max.ringstrom@tetrapak.com>
Date:   Tue Jun 25 08:05:23 2024 +0200

    Inital commit

```

`git log` shows commits and parent commits recursively so that you can see all commits from the specified commit to the first commit of the repository. `git log` is shorthand for `git log HEAD`. `HEAD` is a alias for the currently checked out commit.

There are many options that can be specified in order to modify what to see. Here's an example to show a brief view of the commits.

```
> git log --oneline

13430ab (HEAD -> main) Add introduction to README.md
c7921eb Inital commit

```

### See the commits that exist on the remote branch but not on the local branch
```
git log main..origin/main

commit 98cfed0a715ed783c20e66449b20502377adf704 (origin/main)
Author: Max Ringström <max.ringstrom@tetrapak.com>
Date:   Tue Jun 25 08:23:42 2024 +0200

    Developer A: Add history to README.md
```
This means show all commits between the commit that the local main branch points to and the commit that the remote main branch points to. The command will include the commit on the right side.

Branches are references that point to specific commits. We can see which commits the local and remote branch points to by looking at the top commit:
```
> git log --oneline main
13430ab (HEAD -> main) Developer A: Add introduction to README.md
...
> git log --oneline origin/main
98cfed0 (origin/main) Developer A: Add history to README.md
```

Since branches are just pointers to commits the following commands do the same thing:
```
git log main..origin/main
git log 13430ab..98cfed0
git log main..98cfed0
git log 13430ab..origin/main
```