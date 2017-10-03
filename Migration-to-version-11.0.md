# Before migrating

* Update yourself with the latest OCA Conventions: https://github.com/OCA/maintainer-tools/blob/master/CONTRIBUTING.md
* Subscribe to the mailing list of the related project: https://odoo-community.org/groups
* Announce on the corresponding GitHub issue with the name "Migration to version 11.0" which module(s) you want to migrate

# Tasks to do in the migration

* Bump module version to `11.0.1.0.0`
* Update README.rst from https://raw.githubusercontent.com/OCA/maintainer-tools/master/template/module/README.rst if not updated to the latest template.
* Add tests to increase code coverage.
* Update code to remove use of deprecated methods.
* Do the changes you need to do for making the module works on new version.

# Howto

## Technical method to migrate a module from "10.0" to "11.0" branch

* `$REPO`: the OCA repository hosting the module
* `$MODULE`: the name of the module you want to migrate
* `$USER_ORG`: your GitHub login or organization name

**Full process for beginners in Git flows**

```bash
$ git clone https://github.com/OCA/$REPO -b 11.0
$ git checkout -b 11.0-mig-$MODULE origin/11.0
$ git format-patch --keep-subject --stdout origin/11.0..origin/10.0 -- $MODULE | git am -3 --keep
$ # Adapt the module to the 11.0 version and commit the changes
$ ...
$ git add --all
$ git commit -m "[MIG] $MODULE: Migration to 11.0"
$ git remote add $USER_ORG git@github.com:$USER_ORG/$REPO.git # This mode requires an SSH key in the GitHub account
$ ... or ....
$ git remote add $USER_ORG https://github.com/$USER_ORG/$REPO.git # This will required to enter user/password each time
$ # push the changes to GitHub and make the PR
$ git push $USER_ORG 11.0-mig-$MODULE --set-upstream
```

**Short method for advanced users that know the rest of the Git flow**

```bash
$ git remote update # In case the repo was already cloned before
$ git checkout -b 11.0-mig-<module> origin/11.0
$ git format-patch --keep-subject --stdout origin/11.0..origin/10.0 -- <module path> | git am -3 --keep
```

# Initialization (already done in OCA)

Before migrating the first module, the following tasks must be performed in the repository:

* Create empty 11.0 branch.
* Create metafiles (.travis.yml and README.md) from 10.0, adapting them to have the proper data.
* Make 11.0 default branch.

There's an script to automate all these process in: https://github.com/OCA/maintainer-tools/blob/master/tools/migrate_branch_empty.py.

Some repositories PSCs have decided to keep previous procedure putting installable flag on module as False, using this script: https://github.com/OCA/maintainer-tools/blob/master/tools/migrate_branch.py.
