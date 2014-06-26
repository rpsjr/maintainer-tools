# Migration Launchpad → GitHub

## Migration steps

1. Run the `oca-copy-branches` command to copy the branches on GitHub (https://github.com/OCA/maintainers-tools#usage)
2. On all the Launchpad projects, set the branches of the series to 'abandoned' and create mirror branches from GitHub instead with the `Import a branch` feature. **Manual step** to distribute between maintainers
3. In the meantime, check if projects have series (6.0, 5.0) that were not in the mapping file, and in that case, migrate them with `oca-copy-branches` **Manual step** to distribute between maintainers. The current mapping is here: https://github.com/OCA/maintainers-tools/blob/master/tools/branches.yaml thanks to check for missing branches
4. Add .gitignore, README.md, Travis and Coverage configuration files on the projects. *Script?*
5. Set all the modules of the master branches to `installable: False`. *Script?*
6. Educate developers on the migration of their Launchpad MP to GitHub PR (https://github.com/OCA/maintainers-tools/wiki/How-to-move-a-Merge-Proposal-to-GitHub)
7. Migrate issues? (see https://github.com/termie/lp2gh)

Some of the tasks will be distributed among the maintainers.

## Tasks
* We have to decide where the OCA should deploy its tools (like the script that copies the maintainers in the teams, and maybe others to come)

* a "nag" script for Github (openerp-nag equivalent), but I don't know
Github enough to know if Github is sufficient by itself

* CI installed on all the OCA projects

## Notes on the Launchpad mirroring

When importing a Git branch, Launchpad asks for the branch URL.
It should be in this form: `git://github.com/OCA/connector.git`.
This URL will import the HEAD of `master`. The other branches are imported using the `,branch=BRANCH_NAME` suffix, example: `git://github.com/OCA/connector.git,branch=7.0`.

The git protocol works better than https.



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
