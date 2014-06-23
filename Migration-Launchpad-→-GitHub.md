# Migration Launchpad → GitHub

## Volunteers

* Pedro Manuel Baeza Romero
* Bidoul, Stéphane
* Leonardo Pistone
* Raphael Valyi

## Tasks

* Create a mapping of projects/branches Launchpad → Github that will be
used by the scripts

* At some point, push the head 7.0 branch of each Launchpad project to
their corresponding Github project as the new 8.0. This can be automated
I guess (using the mapping)
* Write a script that mirrors the 6.1 and 7.0 branches to Github and
setup a cron (maybe useless if the branches are moved to GitHub as masters)

* On Github, each project of the OCA has its own list of committers,
there is no way to put the OCA committers automatically in each OCA
project. What we'll do is to maintain 1 committer team [0] and to write
a script (with a cron) that copies all the members in all the others
projects' teams. **done** in https://github.com/OCA/maintainers-tools

* We have to decide where the OCA should deploy its tools (like the
mirroring script and committers script, and maybe others to come)

This is the minimal scope, but we would be more comfortable with:

* a "nag" script for Github (openerp-nag equivalent), but I don't know
Github enough to know if Github is sufficient by itself
* CI installed on all the OCA projects

# How to migrate a project

This is only experimentation. We will maybe use another tool or automate the migration.

### Using https://github.com/felipec/git-remote-bzr

    $ git clone "bzr::lp:sale-wkfl/7.0" sale-wkfl
    Cloning into 'sale-wkfl'...
    $ cd sale-wkfl 
    $ git remote add 6.1 "bzr::lp:sale-wkfl/6.1"                                       
    $ git fetch 6.1
    From bzr::lp:sale-wkfl/6.1
     * [new branch]      master     -> 6.1/master
    $ git branch -va
    * master                94754ca Launchpad automatic translations update.
      remotes/6.1/master    63384dc [MRG] Add mail_quotation
      remotes/origin/HEAD   -> origin/master
      remotes/origin/master 94754ca Launchpad automatic translations update.
    $ git remote add github GITHUB URL
    $ git push github --all
    $ git push github --tags

