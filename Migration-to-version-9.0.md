# Before migrating

* Update yourself with the latest OCA Conventions: https://github.com/OCA/maintainer-tools/blob/master/CONTRIBUTING.md
* Subscribe to the mailing list of the related project: https://odoo-community.org/groups
* Announce on the mailing list which modules you want to migrate

# Todo

* Update README.rst from http://github.com/oca/maintainer-tools
* Add tests to increase code coverage
* Update code to remove use of deprecated methods
* Update code to take advantage of new features
* Bump module version to "9.0.1.0.0"

# Howto

Technical method to migrate "module" from "8.0" to "9.0"

* repo: the OCA repository hosting the module
* myrepo: your fork of the OCA repository
* user: your Github login
* module: the name of the module you want to migrate

Forward port the whole history:

<pre>
$ git clone git@github.com:OCA/repo.git -b 9.0 # (target OCA branch)
$ cd repo
$ git checkout -b 9.0 origin/9.0
$ git merge origin/8.0
</pre>

Or forward port the history of one module:

<pre>
$ git clone git@github.com:OCA/repo.git -b 9.0 # (target OCA branch)
$ cd repo
$ git checkout -b 9.0 origin/9.0
$ ??? # git filter to get the history from 8.0 branch
$ git remote add myrepo git@github.com:user/repo.git
$ git push myrepo 9.0-module
</pre>

# Initialization

Before migrating the first module, the following tasks must be performed:

* Create 9.0 branch from 8.0
* Delete `__unported__` directory
* Update all modules manifest with installable = False
* Update metafiles (.travis.yml and README.md), adapting them from the template in https://github.com/OCA/maintainer-quality-tools/tree/master/sample_files to have the proper data.
* Make 9.0 default branch
* Remove master branch if it still exists