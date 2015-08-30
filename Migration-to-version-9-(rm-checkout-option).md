# Pre-migration tasks

Before migrating the first module of a repository, the following tasks must be performed:

* Create 9.0 branch from 8.0
* Remove all modules
* Update metafiles (.travis.yml, LICENSE, .gitignore, README.md), adapting them from the template in https://github.com/OCA/maintainer-quality-tools/tree/master/sample_files to have the proper data.
* Make 9.0 default branch
* Remove master branch if it exists

Update yourself with the latest OCA Conventions:

https://github.com/OCA/maintainer-tools/blob/master/CONTRIBUTING.md

# Todo
* Update README.rst from http://github.com/oca/maintainer-tools
* Add tests to increase code coverage

# Howto

Technical method to migrate "module" from "8.0" to "9.0"

* repo: the OCA repository hosting the module
* myrepo: your fork of the OCA repository
* user: your Github login
* module: the name of the module you want to migrate

<pre>
$ git clone git@github.com:OCA/repo.git -b 9.0 # (target OCA branch)
$ cd repo
$ git checkout -b 9.0-module
$ git checkout 8.0 -- module
$ git remote add myrepo git@github.com:user/repo.git
$ git push myrepo 9.0-module
</pre>