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
5. Developer A now adds a section on bringing in other people's changes using
merge.

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

`git log` shows commits and parent commits recursively so that you can see all
commits from the specified commit to the first commit of the repository.
`git log` is shorthand for `git log HEAD`. `HEAD` is a alias for the currently
checked out commit.

There are many options that can be specified in order to modify what to see.
Here's an example to show a brief view of the commits.

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
This means show all commits between the commit that the local main branch points
to and the commit that the remote main branch points to. The command will include
the commit on the right side.

Branches are references that point to specific commits. We can see which commits
the local and remote branch points to by looking at the top commit:
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

## Bring in other peoples changes
When other people have made commits to the same branch that you are working on,
you'll have to bring in those changes in some way and modify what you are
working on to fit the new state of the repository.

If the git repository contains source code then your code may depend on code
that someone else has changed, so you need to modify your code accordingly.

### Merge
A common way of bringing in other people's changes is to merge the remote version
of your branch with your local version of the branch.

**You combine your changes with someone else's changes and fix any issues that
arrive in an additional commit.**

A merge is done using the `git merge` command and will create a merge commit
on the branch that you are currently on. You give the second branch to merge
into the current branch as an argument.
```
// With 'main' branch checked out
git checkout main

// Update your cache so you can see the latest state of the remote repository
git fetch

// Bring in remote changes to the branch you are currently on
git merge origin/main

// Solve any inconsistencies
// Stage modified files using 'git add'

// Create the merge commit
git commit
```

#### Example
An example of a merge can be found in this repo. 'Developer B' did a merge in
commit `#25c5ed2`.

Let's take a look at the log as a graph. I've used some flags to the log
command here to simplify the output:

```
> git log --graph --abbrev-commit --pretty=short --date-order 25c5ed2
*   commit 25c5ed2 (HEAD -> main, origin/main)
|\  Merge: d685dbd 98cfed0
| | Author: Developer B <developer.b@company.com>
| |
| |     Merge remote-tracking branch 'origin/main'
| |
* | commit d685dbd
| | Author: Developer B <developer.b@company.com>
| |
| |     Developer B: Describe git log
| |
| * commit 98cfed0
|/  Author: Developer A <developer.a@company.com>
|
|       Developer A: Add history to README.md
|
* commit 13430ab
| Author: Developer A <developer.a@company.com>
|
|     Developer A: Add introduction to README.md
|
* commit c7921eb
  Author: Max Ringström <max.ringstrom@tetrapak.com>

      Inital commit

```

This is how we can interpret the output:
1. The `*` character shows on which branch/path a commit is on
2. The lines show parent commits
3. You can see references that point to a commit in `()`. Here we can see that
`HEAD -> main` and `origin/main` point to the `25c5ed2 ` commit. `HEAD -> main`
means that the local main branch is currently checked out and that it points to
`25c5ed2`.
4. The `25c5ed2` commit has two parents, which means that it is a merge commit.

A merge commit takes all changes made by two chains of commits (a commit and all
ancestor commits) and adds additional modifications to specify what the
repository should look like when the different chains are combined (merged).

At some point in the ancestry of the two chains of commits, the same commit will
exist in both chains. This is called the `base` commit. This is the last commit
before things start to diverge.

If we look at the log output above we can see that the base commit for the merge
was "13430ab Developer A: Add introduction to README.md".

If you have previous experience with git and have solved conflicts, you can often
see three columns in your merge tool, where one of the column is named `Base`.
This column shows what a specific file looked like in the `base` commit.

