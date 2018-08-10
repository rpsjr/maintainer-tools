# Before migrating

* Update yourself with the latest OCA Conventions: https://github.com/OCA/maintainer-tools/blob/master/CONTRIBUTING.md
* Subscribe to the mailing list of the related project: https://odoo-community.org/groups
* Announce on the corresponding GitHub issue with the name "Migration to version 12.0" which module(s) you want to migrate

# Tasks to do in the migration

* Bump module version to `12.0.1.0.0`
* Use new README by fragment system, copying https://github.com/OCA/maintainer-tools/tree/master/template/module/readme and editing or removing the needed sections. You can then run locally `oca-gen-addon-readme` if you have OCA/maintainer-tools if you want to pregenerate the README.rst file (preferred), or keep previous README.rst file.
* Remove any possible migration script from previous version.
* Add tests to increase code coverage.
* If you handle dates and datetimes, you might need to adapt your code now that Odoo returns always native Python objects, so no more `fields.Date/Datetime.from_string` and `fields.Date/Datetime.to_string` is needed.
* Do the rest of the changes you need to do for making the module works on new version.


# Howto

## Technical method to migrate a module from "11.0" to "12.0" branch

* `$REPO`: the OCA repository hosting the module
* `$MODULE`: the name of the module you want to migrate
* `$USER_ORG`: your GitHub login or organization name

**Full process for beginners in Git flows**

* On a shell command:
  ```bash
  $ git clone https://github.com/OCA/$REPO -b 12.0
  $ git checkout -b 12.0-mig-$MODULE origin/12.0
  $ git format-patch --keep-subject --stdout origin/12.0..origin/11.0 -- $MODULE | git am -3 --keep
  ```
* Check https://github.com/OCA/maintainer-tools/wiki/Merge-commits-in-pull-requests for a procedure for reducing commits from "OCA Transbot...".
* Adapt the module to the 12.0 version following tasks to do.
* On a shell command:
  ```bash
  $ git add --all
  $ git commit -m "[MIG] $MODULE: Migration to 12.0"
  $ git remote add $USER_ORG git@github.com:$USER_ORG/$REPO.git # This mode requires an SSH key in the GitHub account
  $ ... or ....
  $ git remote add $USER_ORG https://github.com/$USER_ORG/$REPO.git # This will required to enter user/password each time
  $ git push $USER_ORG 12.0-mig-$MODULE --set-upstream
  ```

**Short method for advanced users that know the rest of the Git flow**

```bash
$ git remote update # In case the repo was already cloned before
$ git checkout -b 12.0-mig-<module> origin/12.0
$ git format-patch --keep-subject --stdout origin/12.0..origin/11.0 -- <module path> | git am -3 --keep
```

**Troubleshooting**

Sometimes, when performing these operations, the process can hang due to conflicts on the patches being applied. One of the possible problems is because a patch removes entirely a file and `git am` is not able to resolve this. It ends with a message `error: ...: patch does not apply`.

If that's your case, you can add `--ignore-whitespace` at the end of the `git am` command for allowing the patch to be applied, although there will be still conflicts to be solved, but they will be doable through standard conflict resolution system. Starting from the previous situation, you will need to do:

```bash
git am --abort
git format-patch --keep-subject --stdout origin/12.0..origin/11.0 -- <module path> | git am -3 --keep --ignore-whitespace
# resolve conflicts (for example git rm <file>)
git add --all
git am --continue
```

# Initialization (already done in OCA)

Before migrating the first module, the following tasks must be performed in the repository:

* Create empty 12.0 branch.
* Create metafiles (.travis.yml and README.md) from 11.0, adapting them to have the proper data.
* Make 12.0 default branch.

There's an script to automate all these process in: https://github.com/OCA/maintainer-tools/blob/master/tools/migrate_branch_empty.py.

Some repositories PSCs have decided to keep previous procedure putting installable flag on module as False, using this script: https://github.com/OCA/maintainer-tools/blob/master/tools/migrate_branch.py.
