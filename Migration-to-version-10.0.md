# Before migrating

* Update yourself with the latest OCA Conventions: https://github.com/OCA/maintainer-tools/blob/master/CONTRIBUTING.md
* Subscribe to the mailing list of the related project: https://odoo-community.org/groups
* Announce on the corresponding GitHub issue with the name "Migration to version 10.0" which module(s) you want to migrate

# Tasks to do in the migration

* Update README.rst from https://raw.githubusercontent.com/OCA/maintainer-tools/master/template/module/README.rst if not updated to latest template.
* Add tests to increase code coverage.
* Update code to remove use of deprecated methods.
* Update code to take advantage of the new features.
* Migrate code to new ORM API if not yet.
* Bump module version to "10.0.1.0.0"
* Replace `select = True` by `index = True`
* Replace string selectors in XML by name (if possible) or other attribute selector or even another equivalent path/reference. For example, change `<group string="x" position="after">` by `<group name="x" position="after">`
* Remove `<data>` and `</data>` in xml files if `noupdate="0"`
* Don't use `@api.one` with `@api.onchange` or it will crash.
* `base.group_configuration` has been renamed to `base.group_system`.
* (more coming in a couple of days...)

# Howto

## Technical method to migrate a module from "9.0" to "10.0" branch

* `<repo>`: the OCA repository hosting the module
* `<module>`: the name of the module you want to migrate
* `<user/org>`: your Github login or organization name

```bash
$ git clone https://github.com/OCA/<repo> -b 9.0 # optional if already existing
$ git remote update # optional if you have just cloned the repo
$ git checkout -b 10.0-mig-<module> origin/10.0
$ git format-patch --stdout origin/10.0..origin/9.0 -- <module> | git am -3
```

# Initialization (already done in OCA)

Before migrating the first module, the following tasks must be performed:

* Create 10.0 branch from 9.0.
* Update all modules manifest with installable = False.
* Update metafiles (.travis.yml and README.md), adapting them from the template in https://github.com/OCA/maintainer-quality-tools/tree/master/sample_files to have the proper data.
* Remove setup folder in the root (if any).
* Rename `__openerp__.py` to `__manifest__.py`.
* Make 10.0 default branch.

There's an script to automate all these process in: https://github.com/OCA/maintainer-tools/blob/master/tools/migrate_branch.py.