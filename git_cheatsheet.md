# Git cheatsheet

## Terminology
- Version Control System (VCS)
- Source Code Management (SCM)

## Data Integrity
Git supports data integrity. You can't change/modify older commits (only the latest you can),
because it keeps the parent commit's hash in commit's metadata.

## History behind git
<details>
  <summary> Click to expand! </summary>

1. Source Code Control System (SCCS)
    - 1972: closed source, free with Unix
    - Stored original version and sets of changes
    - Single user single file
    
2. Revision Control System (RCS)
    - 1982: open source
    - Stored the latest version and sets of changes
    - Single user single file
    
3. Concurrent Version System
    - 1986 - 1990: open source
    - Multiple file, entire project
    - Multi-user repositories
    - Version N of some file
    
4. Apache Subversion (SVN)
    - 2000: open source
    - Track text and images 
    - Track file changes collectively
    - Does snapshots of directory
    - Having a file in revision N
    
5. BitKeeper SCM
    - Distributed Version Control
    - Close Source
    - "Community" free
    - Used for source code of the Linux Kernel from 2002-2005
    - April 2005: the "Community" version was not free anymore
    
6. Git was born
    - April 2005
    - Created by Linus Torvalds
    
## Distributed Version Control

- Local
- Remote
  
   ### Advantages
   - No need to communicate with a central server
   - Faster
   - No network access required
   - Encourages participation and forking projects
   - Developers can work independently
   - Submit change sets for inclusion or rejection

</details>

## Concepts
<details>
<summary> 
The three trees
</summary>

**Working** ⇨ `git add` ⇨ **Staging index** ⇨ `git commit` ⇨ **Repository**

</details>

<details>
<summary>
HEAD~ (tilda) vs HEAD^ (caret) vs HEAD@{} (at sign)
</summary>

```text
ref~ is shorthand for ref~1 and means the commit's first parent. 
ref~2 means the commit's first parent's first parent. 
ref~3 means the commit's first parent's first parent's first parent. And so on.
```

```text
ref^ is shorthand for ref^1 and means the commit's first parent. 
But where the two differ is that ref^2 means the commit's second parent 
(remember, commits can have two parents when they are a merge).
```

      The ^ and ~ operators can be combined.

!["image"](./data/git_tilda_caret.png)
[Reference](https://stackoverflow.com/questions/2221658/whats-the-difference-between-head-and-head-in-git)

```text
Git HEAD@{} or git HEAD followed by the at symbol and curly braces displays 
where the reference or HEAD was pointing at different times in the local repository.
```

      git reflog
   See reference logs, i.e logs of HEAD changes


</details>

<details>
<summary> Reset vs Revert vs Checkout </summary>

| Command      |     Scope     |                                                      Common use cases |
|:-------------|:-------------:|----------------------------------------------------------------------:|
| git reset    | Commit level  | Discard commits in a private branch or throw away uncommitted changes |
| git reset    |  File level   |                                                        Unstage a file |
| git checkout | 	Commit-level |                     	Switch between branches or inspect old snapshots |
| git checkout |  	File-level  |                             	Discard changes in the working directory |
| git revert 	 | Commit-level  |                                      	Undo commits in a public branch |
| git revert 	 |  File-level   |                                                                	(N/A) |

</details>

<details>
<summary> Merge vs Rebase </summary>

- Merge to bring large topic back into main

- Rebase to add minor commits in main to a topic branch

</details>

<details>
<summary> Clean local branches </summary>

    git fetch -p && git branch -vv | awk '/: gone]/{print $1}' | xargs git branch -d

Delete local branches that do not have upstream

</details>


## Basic Commands

<details>
<summary>
git add
</summary>

      git add file
   Will add file to the staging with all it's changes.

      git add -i
   Add changes to the staging in interactive mode. It enters special menu.
   ```text
   What now> s
              staged     unstaged path
     1:    unchanged        +3/-0 file
     2:    unchanged        +2/-0 file2
   
   *** Commands ***
     1: status	  2: update	  3: revert	  4: add untracked
     5: patch	  6: diff	  7: quit	  8: help
   What now>  h
   status        - show paths with changes
   update        - add working tree state to the staged set of changes
   revert        - revert staged set of changes back to the HEAD version
   patch         - pick hunks and update selectively
   diff          - view diff between HEAD and index
   add untracked - add contents of untracked files to the staged set of changes
   ```
   ```
   1: status is equivalent to `git status`
   2: update is equivalent to `git add file`
   3: revert is equivalent to `git restore --staged file`
   4: add untracked is equivalent to `git add file`
   5*: patch does not have equivalent (I think)
   6: diff is equivalent to `git diff file`
   ```
   Patch mode (p)
   
   In patch mode we can select small portion of changes (hunks) for adding to staging state.
   git automatically splits the changes into hunks. Minimum requirement to be able to split into
   the hunks is to have one or more unchanged lines between changes.
   
   there are several commands in patch mode.

   ```text
   y - stage this hunk
   n - do not stage this hunk
   q - quit; do not stage this hunk or any of the remaining ones
   a - stage this hunk and all later hunks in the file
   d - do not stage this hunk or any of the later hunks in the file
   s - split the current hunk into smaller hunks
   e - manually edit the current hunk
   ? - print help
   ```
   
   Noteworthy is a split command. 
   Split is telling git to split the hunk into smaller hunks if possible.
   
   If it is not more possible to split, but we want to prevent to add some parts, we can edit our hunks.
   
   We can enter edit mode (e).

   Important to understand here diff-style prefixes: `+, _, #, <space>`
   ```
   - means deletion
   + means addition
   <space> means no change
   `#` lines starting with number sign will be removed
   ```
   Here are important notes from git

   ```text
   # ---
   # To remove '-' lines, make them ' ' lines (context).
   # To remove '+' lines, delete them.
   # Lines starting with # will be removed.
   #
   # If the patch applies cleanly, the edited hunk will immediately be
   # marked for staging.
   # If it does not apply cleanly, you will be given an opportunity to
   # edit again.  If all lines of the hunk are removed, then the edit is
   # aborted and the hunk is left unchanged.
   ```

      git add -p file
   Directly enters to patch mode for the specified file.

</details>

---

<details>
  <summary> git diff </summary>

>     git diff
> + Difference between **working** and **staging** trees, if **staging** index exists
> + Difference between **working** and **repository** trees, if **staging** index does not exist.


>     git diff --staged
> Difference between **staging** and **repository** trees
> 
> Here we can use `--cached` instead of `--staged` 

>     git diff --color-words
> Show colorized word difference, instead of line difference (default)


>     git diff <commit_hash_1>..<commit_hash_2>
> Difference between two commits. 
>
> ---
>     git diff <commit_hash_1>..HEAD
> In place of <commit_hash_2> can be used HEAD, if we want to compare with the latest commit

>     -S Enter
> Switch text wrapping


</details>

---

<details>
  <summary> git log </summary>

>     git log
> Show commit log

>     git log -n 5
> Show first 5 commits

>     git log --since=2020-05-22 --until=2021-06-15
> Show commits that are between two dates. You can use it also seperately

>     git log --author="Vardan"
> Show commits of specific author

>     git log --grep="text"
> Show filtered commits according to commit message
> 
> In place of the value parameter you can also write regular expression

>     git log --oneline
> Show commits each in one line

>     git log --oneline --graph --decorate
> Show commit graph

>     git log -p"
> Show log with patch (diff)

>     git log -L 1,5:file"
> Show logs only for the specific line of specific file. It automatically applies patch mode
 
</details>

---

<details>
  <summary> git show </summary>

>       git show <commit_hash>
> Show the diff of the commit
> 


</details>

---

<details>
  <summary> git commit </summary>

>       git commit -a
> Commit without staging, but must be tracked files

>       git commit --amend -m "your text here"
> Modify the latest commit. Command will override the commit message.

</details>

---

<details>
  <summary> git checkout </summary>

>       git checkout -- file.txt
> UNDO changes of `file.txt` file in the working tree
> 
> ---
>       git checkout -- .
> UNDO all changes in the working tree
> 
> ---
>       git checkout <commit_hash> -- file.txt
> Retrieve  `file.txt` from the snapshot
> 

</details>

---

<details>
  <summary> git reset </summary>

>       git reset HEAD file.txt
> UNDO changes of `file.txt` file in the staging tree (un-stage the file)
> 
> ---
>       git reset HEAD .
>
>       git reset
> UNDO all changes in the staging tree (un-stage files)
> 
> ---
>       git reset --soft HEAD~1
> Remove last commit from the history and keep in staging
> 
>       git reset --mixed HEAD~1
> Remove last commit from the history and keep in working
> 
>       git reset --hard HEAD~1
> Remove last commit from the history and do not keep anywhere
>       
</details>

---

<details>
<summary>
git restore
</summary>

      git restore file
Restores (UNDO) changes of the file done in working tree 

      git restore --staged file
Restores (UNDO) changes of the file done in staging tree 

git-restore is a tool to revert non-committed changes. 

</details>

---
<details>
  <summary> git revert </summary>

>       git revert <commit_hash>
> UNDO/revert/reverse the commit. It will make a new commit.

        git revert <commit_hash> -m 1
    Revert a merge commit. -m 1 shows the first parent.

</details>

---

<details>
  <summary> git clean </summary>

>       git clean -n
> Shows untracked files to remove
> 
>       git clean -f
> Removes untracked files

</details>

---

<details>
  <summary> git config </summary>

There are three types of configuration scopes in git

1. System level (`/etc/gitconfig`)
   - `git config --system`
2. User level (`~/.gitconfig`)
      - `git config --global`
3. Project level (`Project/.git/config`)
   - `git config


>       git config --global core.excludesfile ~/.gitignore_global
> Globally ignore files
> 
>       git config --global user.name "Name Surname"
> Sets the username
> 
>       git config --global user.email "user@example.com"
> Sets the email 
> 
>       git config --global core.editor "vim"
> Sets the editor 
> 
>       git config --global color.ui true
> Sets the color 
>     
>     git config --global alias.pushd "push -u origin HEAD"
> Define new alias for setting upstream
> 
> 
</details>

---

<details>
    <summary> git rm </summary>

>       git rm --cached file.txt
> Un-track staged file. Need to commit this change
> 
</details>

---

<details>
    <summary> git ls-tree </summary>

>       git ls-tree HEAD
> Show tracked files.
>
> In order to track empty directories, create there `.gitkeep` empty files, to make them non-empty
> 
</details>

---

<details>
    <summary> git mv </summary>

>       git mv file1.txt file2.txt
> Rename file1.txt -> file2.txt
>
> In order to track empty directories, create there `.gitkeep` empty files, to make them non-empty
> 
> You can achieve renaming of the file without `mv` command by the following way
> 
> 1. os remove committed file
> 2. add new file with the same content ( > 50% similarity)
> 3. git rm os removed file
> 3. git status will show renaming
</details>

---

<details>
    <summary> git tag </summary>

> Tag in a git is a named reference to a commit, it makes it easy to find the commit later on.
> 
> Most often tags are used to mark releases (v1.0, v1.1, v2.0, ...)
>
> Usually tags are named using Semantic Versioning like v"major"."minor"."patch"
>    - major : is a version number where you introduced breaking modifications (modifications of your new version are NOT compatible with previous versions);
>    - minor : is a version number that is compatible with previous versions.
>    - patch : is an increment for a bug fix or a patch fix done on your software.
>
> Git supports two types of tags: **lightweight** and **annotated**.
>
> Lightweight tags are just a pointers to a specific commits.
>
> Annotated tags are stored as full objects in git.
>
> They have:
>    - Checksum
>    - Tagger name, email
>    - Tagging date
>    - Message
>
>
>       git tag # or -l or --list
>    List of local tags
>
>       git tag -l "v.*"
>    List all tags that starts with "v."
>   
>       git tag -n
>    List tags and messages
>
>       git tag v1.0
>    Create a lightweight tag on HEAD
>
>       git tag -a v1.1 -m "my new version 1.1"
>    Create an annotated tag on HEAD
>
>       git tag -a key_feature -m "Some information" 6c8fc6e
>    Tag previous commit
>
>       git push origin v1.0
>    Push v1.0 tag to remote.
>
>       git push --tags
>    Push all tags to remote
>
>       git push -d origin v1.0
>    Delete tag from remote
>
>       git push origin :v1.0
>    Another way of deleting tag from remote
>
>       git tag -d v1.0
>    Delete tag from local
>      
>       git fetch --prune --prune-tags 
>    Syncing remote tags with local (will remove local tags if not found in remote)
>
>       git checkout -b quick_fix v1.0
>    Checkout to the tag with creating a branch
>
>       git checkout v1.0
>    If you checkout tag without creating branch, you'll enter Detached HEAD mode.
>      
>       git fetch --tags
>    Fetch tags from remote
>
</details>

---

<details>
    <summary> git branch </summary>

      git branch --merged
   All the commits that are in the listed branches are also in the current branch

      git branch -d feature
   Will delete feature branch, if it has been merged into the current branch, otherwise will  raise message

      git branch -D feature
   Delete feature branch (Delete branch that has not been merged yet)

      git push origin :feature
   Delete remote feature branch

      git push -d origin feature  # --delete
   Delete remote feature branch

   --- 

      git branch -m new_branch_name
   Rename local branch name

      git push origin :old_branch_name new_branch_name
   Change branch name in remote

      git push -u origin new_branch_name
   Set the upstream for new branch

      git push -u origin HEAD
   Alternatively pushing to HEAD is equivalent to pushing to remote branch having the same 
   name as your current name

   ---

      git branch -vv
   Show remote tracking branches

</details>

---

<details>
    <summary> git fetch </summary>
      
   There are three kind of branches in git.
   
   1. Remote branch feature
   2. Local snapshot of the remote branch (origin/feature), also called **remote tracking branch**
   3. Local branch feature

   Stale branch is a remote-tracking branch that no longer tracks anything, because the actual branch in the
   remote repository has been deleted. This can happen if someone deleted remote branch.

      git remote prune origin
   Delete stale remote-tracking branches. Possible to add `--dry-run` to run "test-delete"

      git fetch -p  # --prune
   Delete stale remote-tracking branches (fetch & prune)
   
</details>

---

<details>
<summary>
git merge
</summary>

     git merge -X theirs main
   If you are in feature branch and want to merge main to 
   your branch and keep master changes

     git merge -X ours main
   If you are in feature branch and want to merge main to 
   your branch and keep your changes

      git checkout --ours file
   To take the version of the file that is in current version

      git checkout --theirs file
   To take the version of the file that is in other version
   
</details>

---

<details>
<summary>
git cherry-pick
</summary>
   
   Cherry-picking is changing commits sha. 

     git cherry-pick hash
   Pick the commit and apply the changes to current branch.
   It can cause a conflict after solving conflict `git add` the changes, and
   do `git cherry-pick --continue`
   
      git cherry-pick A^..B
   Cherry pick commits between A and B. A is included.

      git cherry-pick -e hash
   Pick the commit and apply the changes to current branch, but modify 
   the commit message
   

      git cherry-pick -X ours/theirs hash
   Pick the commit but in case of conflicts take specified version.
   Ours means current version, theirs means commit version.

</details>

---

<details>
<summary> Diff patches</summary>

Diff patches are used to share changes using files. They don't have commit history in it.

      git diff sha1..sha2 > output.diff
   Create a diff patch by writing the diff between two commits into the file

      git apply output.diff
   Apply the diff path. It will add those changes into our working directory. We need to add and commit them.

</details>

---

<details>
<summary> Formatted patches</summary>

- Formatted patches export each commit in Unix mailbox format. That format is useful for sending each change via the email.
- In addition to usual git diff it also includes the information about commits, such as commit messages, author, etc...
- Commit history is transferring, but after applying commits are going to be changed.
- It puts one commit per file by default.

      git format sha1..sha2 -o patches
   Export commits between sha1 and sha2 inside `patches` directory. In order to include sha1 commit also we need to start
   from sha1's previous commit.
   
      git format-patch master
   Export all commits on current branch which are not in master branch.

      git format-patch -1 shaX
   Export single commit

      git am patches/0001-something.patch
   Apply formatted patch. It will make a commit.

</details>

---

<details>
<summary> git merge-base </summary>

    git merge-base main feature
   
Find as good common ancestors as possible for a merge.

</details>

---

<details>
<summary> git rebase </summary>

Rebasing means take commits from a branch and replay them at the end of another branch

![img.png](./data/before_rebase_1.png)
    
       git rebase master

We are currently on `new_feature` branch. It uses current branch by default

Graph view after running the command above.

![img.png](./data/after_rebase_1.png)

It changes commit hashes.

The same result will be done by the following command

        git rebase master new_feature

First it checks out to `new_feature` branch, then is doing the rebase


Rebase onto other branch

![img.png](./data/rebase_onto_1.png)

    git rebase --onto master ecommerce new_feature  # git rebase --onto master upstream branch

- master -> new base, can also be a commit hash
- ecommerce -> old base
- new_feature -> branch to move
  

    git reset --hard ORIG_HEAD

Undo a simple rebase, git keeps HEAD before `merge`, `reset`, `rebase` commands in a temporary variable called `ORIG_HEAD`.

    git rebase -i main
Do rebase in interactive mode. Rebase makes todo list, you can pick, squash, edit, etc... commits.


    git rebase -i HEAD~4
Edit commits without moving them to another base. Rebase branch on it's on place.


- Squash means combine change sets, concatenate messages

- If commit has more than one author, it will take first author.

- There should be one pick on the top.


    git pull --rebase

Git pull is fetch and merge, but if we do not want to merge origin/main with main, we can use --rebase option to rebase our
existing commits on top of origin/main.


    git pull --rebase=preserve
If the commits what we are rebasing that have some merge commits, better use preserve in order not to flatten them

</details>

---

<details>
<summary> git blame</summary>

    git blame file
Show annotated file with commit information.

    git blame -L 1,5 file
Show annotated lines of the specific file with commit information.

    git blame -L 1,+5 file
Show annotated lines of the specific file with commit information. first number is the start and second number is the
number of lines to go up or down (negative number)

</details>

---


<details>
<summary> git bisect </summary>

- Helps to identify bad version or revision to find it
- It checks out the code in between the bad and good commits each time.
- We need to mark last good revision and first bad revision


    git bisect start
Start bisect procedure

    git bisect bad <sha, branch, tag>
Mark bad revision

    git bisect good <sha, branch, tag>
Mark good revision

    git bisect reset
Reset when you've done

</details>

## Resources 

   Used but not limited to.

- https://www.linkedin.com/learning/git-essential-training-the-basics/
- https://www.linkedin.com/learning/git-intermediate-techniques/

gitignore: https://github.com/github/gitignore
