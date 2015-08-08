# Pre-migration tasks

Before migrating the first module of a repository, the following tasks must be performed:

* Add 9.0 branch from 8.0
* Remove all modules (git rm)
* Edit .travis.yml and README.md to have the proper branch number
* Make 9.0 default branch
* Remove master branch if it exists

Update yourself with the latest OCA Conventions:

https://github.com/OCA/maintainer-tools/blob/master/CONTRIBUTING.md

# Todo
* 

# Howto

Technical method to migrate "module" from "8.0" to "9.0"

* repo: the OCA repository hosting the module
* myrepo: your fork of the OCA repository
* user: your Github login
* module: the name of the module you want to migrate

<pre>
$ git clone git@github.com:OCA/repo.git -b 9.0 # (target OCA branch)
$ cd repo
$ git checkout -b 8.0 origin/8.0
$ git checkout -b 9.0-module
$ git filter-branch --subdirectory-filter module # (This last step keeps and rewrites the history only for the selected addon.)
$ git filter-branch -f --tree-filter 'mkdir -v module; git mv -k * module' HEAD
$ git rebase 9.0
$ git remote add myrepo git@github.com:user/repo.git
$ git push myrepo 9.0-module
</pre>