# Merge commits in pull requests
It is good practice that every time that you do a pull request, the commit history contains the minimum necessary commit messages.

## Squash you own commits
 If you have worked locally on some feature, and produced a lot of commits in the process to reach to the final state, the rest of the world does not necessarily need to know your intermediate back and forth activity. You may just want to merge all these commits into a single one, and provide a nice summary of the changes that the changes introduce.

In order to do squash your own commits you can use
GIT_EDITOR=<editor> git rebase -i <first commit>
, where <editor> is the editor of your choice (nano, vim, gedit) and <first commit> is the commit from which you want to start the review.

Example:
> GIT_EDITOR=gedit git rebase -i 1949129

This produces:
> pick 1949129 Introduce feature A

> pick d2cf643 Fix bug 1 of feature A

> pick 42bd9e8 Fix bug 2 of feature A

> pick 7f767d5 Fix bug 3 of feature A


You want a single commit with one description only. Then change the lines to:
> pick 1949129 Introduce feature A
> fixup d2cf643 Fix bug 1 of feature A
> fixup 42bd9e8 Fix bug 2 of feature A
> fixup 7f767d5 Fix bug 3 of feature A


## Squash 

