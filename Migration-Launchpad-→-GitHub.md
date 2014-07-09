# Migration Launchpad → GitHub

**Dashboard:** https://docs.google.com/spreadsheets/d/1CfGY7rc60jnpAWvMfNf0pI7fBDAlumwI2jx6f_RJ4_4/edit?pli=1#gid=0

## Migration steps

How to migrate one project:

**Prerequisite**: Install https://github.com/OCA/maintainers-tools#installation

1. Check in the [Mapping  file](https://github.com/OCA/maintainers-tools/blob/master/tools/branches.yaml) if all the branches to migrate have been declared (series 6.0 and 5.0 may be missing). Correct the mapping file if necessary and commit the change. `master` will be the 8.0 branch.

2. If the GitHub branches are not up-to-date compared to Launchpad, run the `oca-copy-branches` command to copy the branches from Launchpad to GitHub (https://github.com/OCA/maintainers-tools#usage).

        $ mkdir -p branches
        $ oca-copy-branches branches --projects OCA/connector --push

3. On the Launchpad project, set the branches of the series to 'abandoned' and create mirror branches from GitHub with the `Import a branch` feature. Then link the series to the mirror branches. (see [Help on the Launchpad mirroring](#help-on-the-launchpad-mirroring))

4. On the Launchpad project, change the description which should now redirect to the GitHub project.

5. Comment out the project in the [Mapping  file](https://github.com/OCA/maintainers-tools/blob/master/tools/branches.yaml) and commit the change.

6. Add .gitignore, README.md, .travis.yml and .coveragerc configuration files on the projects. 

   Follow this example for .gitignore, README.md and .coveragerc: https://github.com/OCA/connector

   Follow these examples for Travis:
   * .travis.yml config for master branch: https://github.com/OCA/connector/blob/master/.travis.yml
   * .travis.yml config for 6.1 and 7.0 branches: https://github.com/OCA/maintainer-quality-tools

7. Set all the modules of the master branches to `"installable": False`:

        grep installable */__openerp__.py -l | xargs sed  "s/[\"|']installable[\"|']: True/'installable': False/" -i

    Mind that some modules might not have the `installable` attribute defined. In those cases you need to edit the `__openerp__.py` file to add it. This command can help you detecting those cases:

        egrep -w 'name|installable' */__openerp__.py

    It's recommended to rename or move the the modules to more easily identify the unported ones and avoid them to be considered by automated tests.
    This script moves all modules into a `__unported__` directory:

        mkdir __unported__
        find . -mindepth 1 -maxdepth 1 -type d -path './[a-z]*' -execdir git mv {} __unported__ \;

8. Post messages on the pending merge proposals informing the authors that now the project is hosted on GitHub and they have to move their MP (but do not "reject" the MP which makes them difficult to track for the authors). Example:

    > This project is now hosted on https://github.com/OCA/connector. Please move your proposal there. This guide may help you https://github.com/OCA/maintainers-tools/wiki/How-to-move-a-Merge-Proposal-to-GitHub


## Tasks

* Communicate and educate developers on the migration, especially of their Launchpad MP to GitHub PR using guides (https://github.com/OCA/maintainers-tools/wiki/How-to-move-a-Merge-Proposal-to-GitHub).

* Migrate issues? (see https://github.com/termie/lp2gh)

* Board has to decide where the OCA should deploy its tools (runbot, script that copies the maintainers in the teams, and maybe others to come)

* a "nag" script for Github (openerp-nag equivalent), but I don't know
Github enough to know if Github is sufficient by itself

* CI installed on all the OCA projects

## Help on the Launchpad mirroring

1. Go to the `Code` tab of the Launchpad project.
1. On the right, click on `Import a branch`
1. Check that the `Owner` is the team of the project
1. In `Branch Name`, put `github-7.0` where 7.0 is the serie
1. Select `Git` and set the URL, example: `git://github.com/OCA/connector.git,branch=7.0`
1. `Request Import`
1. Edit the `7.0` serie and change its branch to the new `github-7.0` branch
1. Repeat that for the other series of the project

Notes on the URL:

When importing a Git branch, Launchpad asks for the branch URL.
It should be in this form: `git://github.com/OCA/connector.git`.
This URL will import the HEAD of `master`. The other branches are imported using the `,branch=BRANCH_NAME` suffix, example: `git://github.com/OCA/connector.git,branch=7.0`.
The git protocol works better than https.

Example: the branches https://code.launchpad.net/~openerp-connector-core-editors/openerp-connector/github-7.0 and https://code.launchpad.net/~gbaconnier-c2c/openerp-connector/github-master.


## Experiments with `git-remote-bzr`

Using https://github.com/felipec/git-remote-bzr

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
    $ git push github refs/remotes/6.1/master:refs/heads/6.1