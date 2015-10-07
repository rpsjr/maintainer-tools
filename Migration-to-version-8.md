# Todo

* Migrate code to use new API
 * Familiarize yourself with the 8.0 API and differences with version 7
 * Read section "Porting from the old API to the new API": https://www.odoo.com/documentation/8.0/reference/orm.html
 * http://odoo-new-api-guide-line.readthedocs.org/en/latest/index.html
* Move files in models, views and data subdirectories
 * Read the OCA conventions "Directories" section for more details: https://github.com/OCA/maintainer-tools/blob/master/CONTRIBUTING.md
* Move icon.png file in /static/description
* Copy and modify the README.rst template from https://github.com/OCA/maintainer-tools/tree/master/template/module
* Update and add tests to increase code coverage http://odoo-new-api-guide-line.readthedocs.org/en/latest/test.html

# Why

To work on OCA modules you will need to setup your work environment. The objective of the setup is to:

* Use the OCA repository as your pull repository to get the latest version of the version 7.0 module code to convert.
* Checkout only the module you wish to work on
* Setup your own or your company’s github account with a fork of the OCA repository that contains the module you want to convert and set it up as your push repository.
* Use your repository as a base to create a pull request (PR) to the OCA repository.
* Keep the history of the 7.0 branch in the new 8.0 branch you will push.

# How

Replace the following variables and brackets with the appropriate values in the git commands below.

* repo: the OCA repository hosting the module
* myrepo: your fork of the OCA repository
* user: your Github login
* module: the name of the module you want to migrate

Create your fork of the OCA repository before you start: https://help.github.com/articles/fork-a-repo/

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