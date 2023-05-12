# Marc's suggested git workflow

This is a simple git workflow that I have found reasonable to use.
Please improve these instructions if you know better.
This workflow is suitable for projects that are "sufficiently small", which is a difficult thing to quantify.
For "larger" projects, another workflow might be better.

## TL;DR

Assuming you have already cloned the repository:

    cd <repo>
    git switch -c work
    # If that doesn't work because your installation of git is too old,
    # try:
    #   git checkout -b work

    ... do your writing ...
    ... Make lots of small commits ...
    ... When you're ready to share ...

    git checkout master
    git pull
    git rebase master work
    git checkout master
    git merge --ff-only work
    git push
    git branch -d work

## Terminology

**repository**: the "database" used to manage a set of files. A `git`
repository usually resides in a (hidden) directory named `.git`; you
probably will never need to touch this directory or files in it
directly.  
**working tree**: the set of files and directories in your filesystem
which are under management with `git`.  
**clean**: a working tree is *clean* if it matches the `HEAD` of your
repository; that is, you have no changes in files that are not recorded
in the `git` repository.

## Getting started

To get started, you need to make a local copy, or *clone*, of a remote
(central) repository. This gives you a *full* (local) copy of the remote
repository. Most of the `git` commands you will use will access and
modify your local repository; `git pull` and `git push` are the two
major exceptions.

For a project hosted at the Fermilab Redmine site, there should be an
associated `git` repository accessible with the following command.

    git clone ssh://p-SOMEPROJECT@cdcvs.fnal.gov/cvs/projects/SOMEPROJECT

For other projects, you have to know the URL of the git repository you
wish to clone.

This will create a directory named `SOMEPROJECT` under your current
working directory; this directory will contain a copy of the SOMEPROJECT
repository. Since this gives you a *complete* local copy of the
repository, you can make whatever changes you want to your repository
without having any effect on the central repository you just cloned.

## Making local changes

In order to make it easy to work incrementally on changes to
`SOMEPROJECT`, and to avoid collisions with others' work, I find it
useful to do essentially all my development on a *branch*. By doing the
same, you can make speculative changes of whatever scale you want, and
decide when you are done whether to keep those changes (by *merging* the
changes back onto your `master` branch, after updating your branch to a
state that makes it "as if" your changes had all been added in one
change; this is done with `git rebase`, as described below), or to
abandon those changes (by not doing anything, except possibly deleting
your abandoned branch). It is easy to keep several branches under
development at the same time, if you are working on unrelated items in
the same repository (*e.g.* changes to two different documents). Any
branches you make are visible only locally, to you, not to others
sharing use of the central `SOMEPROJECT` repository.

This technique of making all your changes on a local branch, and
updating with `rebase` and `merge` takes a few more steps than working
directly on the `master` branch. It has the virtue of avoiding almost
all merge collisions, and it keeps the history of changes in the central
repository very easy to understand. Please do not take the short-cut of
working directly on the master; that may make you avoid a few commands
in the short term, but is certain to eventually cause much more work
both for yourself and for others.

### Finding out what is your active branch

To find out what is your active branch, use:

    git branch

This lists all the branches in your repository, and indicates the
current branch with an asterisk. If you also want to see the branches
that reflect the branches in the "central" repository, use
`git branch -a`.

### Creating a branch

To create a new branch, and simultaneously make it your currently active
branch, use:

    git checkout -b name-of-my-new-branch

where *name-of-my-new-branch* should be replaced by the name you want
your new branch to have. This is going to be a short-lived branch; I
usually use the name `work`.

This both creates the branch (in your repository) and makes it be your
currently active branch. This command does not modify your working tree.
**You should use this command only if your working tree is clean**.

### Checking to see if your working tree is clean

To find out if your working tree is clean, use:

    git status

This will tell you of any files you have modified, or any files you've
added without committing.

### Changing your current branch

You can change freely between branches, if you've committed all your
changes to your current branch. The command to change your current
branch, to an already existing branch, is:

    git checkout name-of-already-existing-branch

Note the *lack* of the `-b`, which would create a new branch and move to
it.

This command will fail, with an error message, if you have changes to
your working tree that have not been committed. So, before switching to
a different branch, make sure to commit all your changes. If you want to
abandon the changes, you can use `git checkout name-of-file-to-restore`
to restore the repository version of a file you have modified. If you
have many files to restore, you might use `git reset --hard`. Be
careful, because you can't recover your changes if you lose changes if
files you didn't mean to lose.

### Saving the results you like

When you have a set of changes you want to keep as your main line of
development, you need to merge the branch containing those changes back
into the `master` branch.

1.  Commit all the changes in your working tree to your development
    branch
2.  Set your working branch to be `master`
3.  Get any updates to `master` from the central repository, using
    `git pull`
4.  If the `git pull` downloads any new commits from the central
    repository, update your branch so that it's changes are added after
    the ones you just obtained. This is done with
    `git rebase master name-of-working-branch`,
    where `name-of-working-branch`
    should be replaced by the actual name of your working branch.
    If the modifications you just obtained with the `pull`
    do not collide with any of your changes, the `rebase` will succeed
    (possibly with a few messages about automated merges being made). If
    there is a collision that has to be handled "by hand", see the
    instructions on resolving a rebase collision below. After resolving
    any collisions, continue with the next step.
5.  Then merge the development branch into the master, with:
    `git merge --ff-only name-of-development-branch`

The `-ff-only` helps make sure the additions you are making will not
cause any merge conflicts. If you have followed the procedure above, it
should succeed.

Once you have merged your changes back into your master branch, you can
delete the development branch without losing any work:

    git branch -d name-of-develoment-branch

### Resolving a rebase collision

When the `git rebase x y` command is unable to automatically merge
changes, it will tell you with a detailed message, which ends in
something like:

    When you have resolved this problem, run "git rebase --continue".
    If you prefer to skip this patch, run "git rebase --skip" instead.
    To check out the original branch and stop rebasing, run "git rebase --abort".   

At this point, `git status` will tell you that you are not on any
branch. This is the indication that you have some merges to resolve "by
hand". `git status` will also tell you what file(s) need to be fixed.
`git` will have annotated those files with "change markers", like:

    <<<<<<< HEAD

  
which appears at the *start* of the section of the file changed in the
HEAD of the repository (what you've just `pulled` from the central
repository, if you're following these instructions), and

    =======

  
which divides the `pull`-ed changes from your local changes, and

    >>>>>>> Fix spelling

  
which ends your local changes. The text "Fix spelling" is the commit
message I used in my example; you should see your own commit message.

To complete the merge, edit the file with whatever editor you prefer,
producing the text you want to have as the resolution of the conflict.
When you are done, the git change markers should be removed.

Then do a `git add` of that file, followed by `git rebase --continue`.

You may have to repeat this several times, for several different files
(or even for several different collisions in the same file). When all
the collisions are resolved, continue with the steps above.

### Forgetting about changes you don't like

If you decide your development branch was junk, and want to get rid of
it, *do not merge it back into master*. Instead, you can either:

1.  leave it be, because it doesn't take much room in your repository
    and you may later decide it was good after all, or
2.  throw the branch away, with
        git branch -D name-of-development-branch

      
    Note the capital letter D: this will delete a branch that contains
    work not otherwise saved, so be careful with it.

### Sending your changes back to the central repository

After you have merged your changes onto your local master branch, you
may want to send those changes back to the central repository. First,
you should always make sure to download any changes from the central
repository to your repository. Second, you can upload your changes.

To download changes from the central repository to your repository, use:

    git pull

To upload your changes to the central repository, use:

    git push

## Comparing your working tree to the remote (central) repository

To compare your working tree to the remote (central) repository from
which it was cloned, one first fetches the changes from the remote
repository into your local clone — but *without* merging those changes
into the `master` branch of your own repository. To do this, use *git
fetch* (not *git pull*, which was used above).

    git fetch

  
Now the branch your local repository has as *origin/master* matches what
is in the *master* branch of the shared remote repository.  
To compare your working tree to this branch, use the "triple dot" syntax
of *git diff*:

    git diff origin/master ... master

  
If your version of git is too old to have this shorthand, then you can
do it "by hand":

    git merge-base origin/master master
    # some long SHA1 is returned
    git diff the-SHA1-from-above

  
Either of these command sets (the triple-dot diff or the two-step
merge-base + diff incantation) shows the changes that have been
introduced to the remote (shared) repository since you last merged with
it. After you have convinced yourself that you want to merge the changes
from the remote repository into your master branch, you should use *git
merge* to do so:

    git merge origin/master
