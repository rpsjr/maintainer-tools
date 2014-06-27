# Migration Launchpad → GitHub

## Migration steps

How to migrate one project:

**Prerequisite**: Install https://github.com/OCA/maintainers-tools#installation

1. Check in the [Mapping  file](https://github.com/OCA/maintainers-tools/blob/master/tools/branches.yaml) if all the branches to migrate have been declared (series 6.0 and 5.0 may be missing). Correct the mapping file if necessary and commit the change.

2. Run the `oca-copy-branches` command to copy the branches on GitHub (https://github.com/OCA/maintainers-tools#usage)

        $ mkdir branches
        $ oca-copy-branches branches --projects OCA/connector --push

3. On the Launchpad project, set the branches of the series to 'abandoned' and create mirror branches from GitHub with the `Import a branch` feature. Then link the series to the mirror branches. (see **Help on the Launchpad mirroring**)

4. Add .gitignore, README.md, Travis and Coverage configuration files on the projects. Example: https://github.com/OCA/connector/pull/2 . Launchpad series were transposed to a github branch, so you may have to repeat operation for each github branch.

5. Set all the modules of the master branches to `installable: False` and rename the modules with a `_unported` suffix so they won't be tested by Travis

        grep installable */__openerp__.py -l | xargs sed  "s/[\"|']installable[\"|']: True/'installable': False/" -i
        find . -mindepth 1  -maxdepth 1 -type d -not -path '*/\.*'  -execdir git mv {} {}_unported \;

6. Post messages on the pending merge proposals informing the authors that now the project is hosted on GitHub and they have to move their MP. Example:

    > This project is now hosted on https://github.com/OCA/connector. Please move your proposal there. This guide may help you https://github.com/OCA/maintainers-tools/wiki/How-to-move-a-Merge-Proposal-to-GitHub

7. Comment out the project in the [Mapping  file](https://github.com/OCA/maintainers-tools/blob/master/tools/branches.yaml) and commit the change.


## Tasks

* Communicate and educate developers on the migration, especially of their Launchpad MP to GitHub PR using guides (https://github.com/OCA/maintainers-tools/wiki/How-to-move-a-Merge-Proposal-to-GitHub).

* Migrate issues? (see https://github.com/termie/lp2gh)

* We have to decide where the OCA should deploy its tools (like the script that copies the maintainers in the teams, and maybe others to come)

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