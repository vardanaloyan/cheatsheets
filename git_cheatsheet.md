# Git cheatsheet

resource: https://www.linkedin.com/learning/git-essential-training-the-basics/

gitignore: https://github.com/github/gitignore

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

## The three trees

> **Working** ⇨ `git add` ⇨ **Staging index** ⇨ `git commit` ⇨ **Repository**

## Basic Commands
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
> UNDO all changes in the staging tree (un-stage files)


</details>

---

<details>
  <summary> git revert </summary>

>       git revert <commit_hash>
> UNDO/revert/reverse the commit. It will make a new commit.

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
>       git config --global user.email "user@example.com"
> Sets the email 
> 
>       git config --global user.email "user@example.com"
> Sets the email 
> 
>       git config --global core.editor "vim"
> Sets the editor 
> 
>       git config --global color.ui true
> Sets the editor 
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
