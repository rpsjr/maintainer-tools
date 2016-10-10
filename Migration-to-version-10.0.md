# Before migrating

* Update yourself with the latest OCA Conventions: https://github.com/OCA/maintainer-tools/blob/master/CONTRIBUTING.md
* Subscribe to the mailing list of the related project: https://odoo-community.org/groups
* Announce on the corresponding GitHub issue with the name "Migration to version 10.0" which module(s) you want to migrate

# Tasks to do in the migration

* Bump module version to "10.0.1.0.0"
* Migrate code to new ORM API if not yet.
* Replace `openerp` imports to `odoo`. You can use this command:

  ```bash
  find . -type f -name '*.py' | xargs sed -i 's/from openerp import/from odoo import/g'
  ```
* Rename `__openerp__.py` to `__manifest__.py` if not yet renamed.
* Update README.rst from https://raw.githubusercontent.com/OCA/maintainer-tools/master/template/module/README.rst if not updated to the latest template.
* Add tests to increase code coverage.
* Update code to remove use of deprecated methods.
* Update code to take advantage of the new features.
* Replace `select = True` by `index = True`
* Replace string selectors in XML by name (if possible) or other attribute selector or even another equivalent path/reference. For example, change `<group string="x" position="after">` by `<group name="x" position="after">`
* Remove `<data>` and `</data>` in xml files if `noupdate="0"`
* Don't use `@api.one` with `@api.onchange` or it will crash.
* `base.group_configuration` has been renamed to `base.group_system`.
* (more coming in a couple of days...)

# Howto

## Technical method to migrate a module from "9.0" to "10.0" branch

* `$REPO`: the OCA repository hosting the module
* `$MODULE`: the name of the module you want to migrate
* `$USER-ORG`: your GitHub login or organization name

```bash
$ git clone https://github.com/OCA/$REPO -b 9.0 # optional if already existing
$ git remote update # optional if you have just cloned the repo
$ git checkout -b 10.0-mig-$MODULE origin/10.0
$ git format-patch --stdout origin/10.0..origin/9.0 -- $MODULE | git am -3
$ # Adapt the module to the 10.0 version and commit the changes
$ ...
$ git add --all
$ git commit -m "[MIG] $MODULE: Migrated to 10.0"
$ # optional if you already have your remote configured
$ git remote add $USER-ORG git@github.com:$USER-ORG/$REPO.git # This mode requires an SSH key in the GitHub account
$ git remote add https://github.com/$USER-ORG/$REPO.git # This will required to enter user/password each time
$ # push the changes to GitHub and make the PR
$ git push $USER-ORG 10.0-mig-$MODULE --set-upstream
```

# Initialization (already done in OCA)

Before migrating the first module, the following tasks must be performed in the repository:

* Create 10.0 branch from 9.0.
* Update all modules manifest with installable = False.
* Update metafiles (.travis.yml and README.md), adapting them from the template in https://github.com/OCA/maintainer-quality-tools/tree/master/sample_files to have the proper data.
* Remove setup folder in the root (if any).
* Rename `__openerp__.py` to `__manifest__.py`.
* Make 10.0 default branch.

There's an script to automate all these process in: https://github.com/OCA/maintainer-tools/blob/master/tools/migrate_branch.py.