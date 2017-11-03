# Before migrating

* Update yourself with the latest OCA Conventions: https://github.com/OCA/maintainer-tools/blob/master/CONTRIBUTING.md
* Subscribe to the mailing list of the related project: https://odoo-community.org/groups
* Announce on the mailing list which modules you want to migrate

# Todo

* Update README.rst from https://raw.githubusercontent.com/OCA/maintainer-tools/master/template/module/README.rst
* Remove any possible migration script from previous version.
* Add tests to increase code coverage
* Update code to remove use of deprecated methods
* Update code to take advantage of the new features
* Migrate code to new ORM API.
* Bump module version to "9.0.1.0.0"
* Replace `select = True` by `index = True`
* Replace string selectors in XML by name (if possible) or other attribute selector or even another equivalent path/reference. For example, change `<group string="x" position="after">` by `<group name="x" position="after">`
* Remove `<data>` and `</data>` in xml files if `noupdate="0"`

# Howto

## Technical method to migrate a module from "8.0" to "9.0" branch

* `<repo>`: the OCA repository hosting the module
* `<module>`: the name of the module you want to migrate
* `<user/org>`: your Github login or organization name

```bash
$ git remote update # In case the repo was already cloned before
$ git checkout -b 9.0-mig-<module> origin/9.0
$ git format-patch --stdout origin/9.0..origin/8.0 -- <module path> | git am -3
```

# Initialization (already done in OCA)

Before migrating the first module, the following tasks must be performed:

* Create 9.0 branch from 8.0
* Delete `__unported__` directory
* Update all modules manifest with installable = False
* Update metafiles (.travis.yml and README.md), adapting them from the template in https://github.com/OCA/maintainer-quality-tools/tree/master/sample_files to have the proper data.
* Make 9.0 default branch
* Remove master branch if it still exists