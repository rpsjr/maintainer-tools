# Before migrating

* Update yourself with the latest OCA Conventions: https://odoo-community.org/page/contributing
* Subscribe to the mailing list of the related project: https://odoo-community.org/groups
* Announce on the corresponding GitHub issue with the name "Migration to version 13.0" which module(s) you want to migrate

# Tasks to do in the migration

* Bump module version to `13.0.1.0.0`.
* Remove any possible migration script from previous version.
* Squash administrative commits (if any) with the previous commit for reducing commit noise. They are named as "[UPD] README.rst", "[UPD] Update $MODULE.pot", "Update translation files" and similar names, and comes from *OCA-git-bot*, *oca-travis* or *oca-transbot*.
* Remove all the decorators `@api.multi`, `@api.returns`, `@api.one`, `@api.cr`, `@api.model_cr` from the code. Now they are all multi-record by default. In case of the last ones, you will need to adapt the code to the behavior change. 
* Check that all "compute" methods of non-stored computed fields assign a value in any case to the field, even if it is a "False". (https://github.com/odoo/odoo/pull/36743/commits/2e43bfc1c4b2f61e0459614f61f90a77dc3b7233). Stored fields can keep the previous value.
* Replace sudo(user): "deprecated use of sudo(user), use with_user(user) instead"
* Some of the Font Awesome (FA) icons have changed their name as now Odoo uses FA v5, so you might need to change them in your module views. Check the changed names in https://fontawesome.com/how-to-use/on-the-web/setup/upgrading-from-version-4#name-changes.
* Remove all the `oldname` field attributes in the code. If they were added in previous version, they have served their function any way, and now in this version it's not supported, so if you have the need, create a migration script and use openupgradelib's `rename_fields` method.
* Remove `view_type` tag on action window XML definition. It's now always `form` (tree is not supported since 11.0 any way).
* Add tests to increase code coverage.
* Check tasks of previous versions if you are migrating from lower versions than v12. It's also recommended to check for things not done in previous migrations.
* Do the rest of the changes you need to do for making the module works on new version.

Regex which can help to find the things to remove/change:

```
grep -nri 'oldname\|sudo([^\)]\+)\|api.multi\|api.returns\|api.one\|api.cr\|api.model_cr'
```

# Howto

## Technical method to migrate a module from "12.0" to "13.0" branch

* `$REPO`: the OCA repository hosting the module
* `$MODULE`: the name of the module you want to migrate
* `$USER_ORG`: your GitHub login or organization name

**Full process for beginners in Git flows**

* On a shell command:
  ```bash
  $ git clone https://github.com/OCA/$REPO -b 13.0
  $ git checkout -b 13.0-mig-$MODULE origin/13.0
  $ git format-patch --keep-subject --stdout origin/13.0..origin/12.0 -- $MODULE | git am -3 --keep
  ```
* Check https://github.com/OCA/maintainer-tools/wiki/Merge-commits-in-pull-requests for a procedure for reducing commits from "OCA Transbot...".
* Adapt the module to the 13.0 version following tasks to do.
* On a shell command:
  ```bash
  $ git add --all
  $ git commit -m "[MIG] $MODULE: Migration to 13.0"
  $ git remote add $USER_ORG git@github.com:$USER_ORG/$REPO.git # This mode requires an SSH key in the GitHub account
  $ ... or ....
  $ git remote add $USER_ORG https://github.com/$USER_ORG/$REPO.git # This will required to enter user/password each time
  $ git push $USER_ORG 13.0-mig-$MODULE --set-upstream
  ```

**Short method for advanced users that know the rest of the Git flow**

```bash
$ git remote update # In case the repo was already cloned before
$ git checkout -b 13.0-mig-<module> origin/13.0
$ git format-patch --keep-subject --stdout origin/13.0..origin/12.0 -- <module path> | git am -3 --keep
```

**Troubleshooting**

Sometimes, when performing these operations, the process can hang due to conflicts on the patches being applied. One of the possible problems is because a patch removes entirely a file and `git am` is not able to resolve this. It ends with a message `error: ...: patch does not apply`.

If that's your case, you can add `--ignore-whitespace` at the end of the `git am` command for allowing the patch to be applied, although there will be still conflicts to be solved, but they will be doable through standard conflict resolution system. Starting from the previous situation, you will need to do:

```bash
git am --abort
git format-patch --keep-subject --stdout origin/13.0..origin/12.0 -- <module path> | git am -3 --keep --ignore-whitespace
# resolve conflicts (for example git rm <file>)
git add --all
git am --continue
```

# Initialization (already done in OCA)

Before migrating the first module, the following tasks must be performed in the repository:

* Create empty 13.0 branch.
* Create metafiles (.travis.yml and README.md) from 12.0, adapting them to have the proper data.
* Make 13.0 default branch.

There's an script to automate all these process in: https://github.com/OCA/maintainer-tools/blob/master/tools/migrate_branch_empty.py.

Some repositories PSCs have decided to keep previous procedure putting installable flag on module as False, using this script: https://github.com/OCA/maintainer-tools/blob/master/tools/migrate_branch.py.
