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
6. Developer A adds a section describing how to bring in other people's changes
using rebase.
7. Developer B explains how to see the changes made by a commit
8. Developer B explains how to find out the commit that was the last to modify
a line in a file.
9. Developer A adds an example showing how Developer B did a rebase in order
to deliver the previous two commits.
10. Developer A explains what git cherry-pick does
11. Developer A starts on giving an example of a cherry-pick but doesn't finish
    the work yet.

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

### See changes made by a commit
```
> git show 13430ab
commit 13430ab22e3354ca168a1213c529f1a1e464af72
Author: Developer A <developer.a@company.com>
Date:   Wed Jun 26 08:25:53 2024 +0200

    Developer A: Add introduction to README.md

diff --git a/README.md b/README.md
index e69de29..5afebdc 100644
--- a/README.md
+++ b/README.md
@@ -0,0 +1,4 @@
+# Introduction
+This is a git repository that is used for educational
+purposes in order to learn key git concepts and
+typical work flows.

```

The `git show` command can be used to show information about a commit. It also
includes the file modifications that a commit does. In the above output we can
see that the README.md file was modified. `a/filename` refers to the file before
the commit was applied and `b/filename` the commit's modifications.

### See which commit was the last to modify a line
Sometimes it might be of interest to get more information about one or more lines
in a file. You might not understand why they are the way they are or you might
want to know when a change was made. If you're into pointing fingers then you
might want to know who was responsible for a specific change to a file.

This can be achieved by using the rather aggressively named command 'git blame'.
```
> git blame README.md
13430ab2 (Developer A 2024-06-26 08:25:53 +0200   1) # Introduction
13430ab2 (Developer A 2024-06-26 08:25:53 +0200   2) This is a git repository that is used for educational
13430ab2 (Developer A 2024-06-26 08:25:53 +0200   3) purposes in order to learn key git concepts and
13430ab2 (Developer A 2024-06-26 08:25:53 +0200   4) typical work flows.
...
```
This will show you all lines in the README.md file and annotate each line with
last commit to modify the line as well as the author of the commit and a time stamp.

You can then use `git show` on the commit to see the commit message for the commit
which might give you a better understanding on why the line was modified.

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

### Rebase
When someone else has added commits to the remote branch the problem is that
you have based your work on a commit that is not the newest commit.

You could reset your branch so that it points to the latest remote commit and then
redo your work by modifying the files and creating new commits for what you had
previously done. Then your commit will be based on the latest commit on the remote
branch and you will be able to push your work without any problems.

This is basically what a rebase does for you. It resets you current branch
to the commit that you rebase **on** and applies all your commits **one by one**
in the order that they were originally created.

Since the repository no longer looks the same as it did when the commits where
first created you have to modify your changes so that they work correctly given
the new state of the repository. In a rebase this is done **per commit** when
they are re-applied. Compare this to a *merge* where any inconsistencies are fixed
in one single commit placed **after** your commits and the new commits on the remote
branch.

This is how you do a rebase:
```
// You are on a branch, such as the local main branch
git checkout main

// Update the cache so that you can reference the latest commits on the remote
// repository
git fetch

// Rebase your work on the remote main branch
git rebase origin/main

// Git will automatically reset your branch to the origin/main commit and
// apply your commits one by one (creating new commits doing the same changes).
// Git will halt the process if any of your old commits modify the same files
// as the new commits on the origin/main branch.

// Git halts before applying the old commit and modifies the working tree so that
// all file changes that were originally made are present.

// Fix the conflicts
// Stage modified files using git add

// Commit the new modified version of the commit that failed
git commit

// The above steps continue until all old commits have been re-applied.
```

#### Example
Developer B did a rebase when delivering `a604f6e`.

The advantage and disadvantage with doing a rebase is that you modify the history
of your branch(!). So now that we look at the commit log, it will look as if
Developer B always had the latest commits on their branch and never had to bring
in other people's changes.

```
>  git log --graph --oneline --abbrev-commit a604f6e
* a604f6e (HEAD -> main, origin/main) Explain how to see the last commit to modify a line
* d53eff7 Explain how to see a commit's modifications
* 2729faa Developer A: Describe 'git rebase'
* ebcbfb5 (tag: old_base) Developer A: Describe 'git merge'
*   25c5ed2 Merge remote-tracking branch 'origin/main'
|\
| * 98cfed0 Developer A: Add history to README.md
* | d685dbd Developer B: Describe git log
|/
* 13430ab Developer A: Add introduction to README.md
* c7921eb Inital commit

```

But if we look at the commits that where on the branch before the rebase along
with the rebased commits we can see what has happened.

```
> git log --graph --date-order --abbrev-commit --oneline commit_before_rebase commit_after_rebase
* a604f6e (HEAD -> main, tag: commit_after_rebase, origin/main) Explain how to see the last commit to modify a line
* d53eff7 Explain how to see a commit's modifications
| * 2e62304 (tag: commit_before_rebase) Explain how to see the last commit to modify a line
| * c385154 Explain how to see a commit's modifications
* | 2729faa (tag: new_base) Developer A: Describe 'git rebase'
|/
* ebcbfb5 (tag: old_base) Developer A: Describe 'git merge'
*   25c5ed2 Merge remote-tracking branch 'origin/main'
|\
* | d685dbd Developer B: Describe git log
| * 98cfed0 Developer A: Add history to README.md
|/
* 13430ab Developer A: Add introduction to README.md
* c7921eb Inital commit

```

Take a look at the tags that has been added for clarity:
| Tag | Description |
| :--- | :--- |
| old_base | The commit that Developer B first based their work on. |
| commit_before_rebase | The last commit that Developer B did before realising that someone else had pushed commits to the remote branch. |
| new_base | The latest commit on the remote branch when Developer B started their rebase. |
| commit_after_rebase | The new latest commit after Developer B has `rebased`their work on the latest commit on the remote branch. |

What you should take note of here is:
- The commits before the rebase still exist, but the branch no longer has them
on it.
- After the rebase there will be new commits with the same commit message but
other IDs. This is because the rebase re-applies the old commits on the new
base commit, thus creating new commits. These new commits should do the same
thing that the old commits did, but may need to do it differently due to how the
new commits on the remote branch has modified the repository since they were first
created.

**WARNING**: When you do a rebase you change your branche's history. If someone
else is working on the same branch as you, then when you push your changed history, 
you will make the history of that branch look different on their remote branch
as compared to their locally checked out branch.

Remember that you create new commits for each of the old commits on your branch.
Any person that works on the same branch after you have pushed your new commits
will see the old commits on their branch and the new commits on the remote branch.

They will see what looks like duplicate commits.

There are ways to handle this but they require good knowledge about git so a
good policy is to **Never rebase a branch that you share with someone else**.

This policy should be followed until all involved parties understand exactly
what a rebase does, are fluent in looking at commit graphs and commit
modifications, and are comfortable with doing what is called an interactive rebase.

### Cherry-pick
Sometimes you want to get changes made by one or more commits found on another
branch, without bringing in all changes on that branch.

It might be that the other branch is still being worked on or that a bug fix has
been delivered to a release branch for an old version of your project. In the
second case you might want to get that specific commit on your new version's
main branch. 

You can do this using the `git cherry-pick` command which takes the file
modifications and commit message from a commit and creates a new commit
with that information on your current branch.

#### Example
This is how you can cherry-pick a commit:
```
// TODO - Add example here
```