@ Before migrating

* Update yourself with the latest OCA Conventions: https://github.com/OCA/maintainer-tools/blob/master/CONTRIBUTING.md

# Todo
* Migrate code to use new API
* Move files in models, views and data subdirectories
* Move icon.png file in /static/description
* Use README.rst from http://github.com/oca/maintainer-tools
* Add tests to increase code coverage

# Howto

Technical method to migrate "module" from "7.0" to "8.0"

* repo: the OCA repository hosting the module
* myrepo: your fork of the OCA repository
* user: your Github login
* module: the name of the module you want to migrate

<pre>
$ git clone git@github.com:OCA/repo.git -b 8.0 # (target OCA branch)
$ cd repo
$ git checkout -b 7.0 origin/7.0
$ git checkout -b 8.0-module
$ git filter-branch --subdirectory-filter module # (This last step keeps and rewrites the history only for the selected addon.)
$ git filter-branch -f --tree-filter 'mkdir -v module; git mv -k * module' HEAD
$ git rebase 8.0
$ git remote add myrepo git@github.com:user/repo.git
$ git push myrepo 8.0-module
</pre>

# Initialization

Before migrating the first module of a repository, the following tasks must be performed:

* Add 8.0 branch from 7.0
* Move all modules to `__unported__/`
* Edit .travis.yml and README.md to have the proper branch number
* Make 8.0 default branch
* Remove master branch