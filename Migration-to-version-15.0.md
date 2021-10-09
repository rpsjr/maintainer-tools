# Before migrating

* Update yourself with the latest OCA Conventions: https://odoo-community.org/page/contributing
* Subscribe to the mailing list of the related project: https://odoo-community.org/groups
* Announce on the corresponding GitHub issue with the name "Migration to version 15.0" which module(s) you want to migrate

# Tasks to do in the migration

* Bump module version to `15.0.1.0.0`.
* Remove any possible migration script from previous version.
* Squash administrative commits (if any) with the previous commit for reducing commit noise. They are named as "[UPD] README.rst", "[UPD] Update $MODULE.pot", "Update translation files" and similar names, and comes from *OCA-git-bot*, *oca-travis* or *oca-transbot*. **IMPORTANT**: Don't squash legit translation commits, authored by their translators, with the message "Translated using Weblate (...)".
* `t-raw` QWeb directives are replaced by `t-out` ones, with the optional Python tool `markupsafe.Markup` for escaping HTML content in server side. HTML fields apply directly the markup when used on QWeb. See https://github.com/odoo/odoo/commit/01875541b1a8131cb for more details.
* The access to ir.model* objects has been removed, so you need to use `sudo`, or use the existing methods for getting usual data. See https://github.com/odoo/odoo/pull/69120 for more info.
* Replace `SavepointCase` by `TransactionCase` in tests, as they are now the same. The old one still exists as an alias, but a warning will arise, and next version will remove such alias. More info at https://github.com/odoo/odoo/pull/62031
* Add tests to increase code coverage.
* Check tasks of previous versions if you are migrating from lower versions than v14. It's also recommended to check past migration guides for things not done in previous migrations.
* Do the rest of the changes you need to do for making the module works on new version.

# Howto

## Technical method to migrate a module from "14.0" to "15.0" branch

* `$REPO`: the OCA repository hosting the module
* `$MODULE`: the name of the module you want to migrate
* `$USER_ORG`: your GitHub login or organization name

**Full process**

* On a shell command:
  ```bash
  $ git clone https://github.com/OCA/$REPO -b 15.0
  $ git checkout -b 14.0-mig-$MODULE origin/15.0
  $ git format-patch --keep-subject --stdout origin/15.0..origin/14.0 -- $MODULE | git am -3 --keep
  $ pre-commit run -a  # to run black, isort and prettier (ignore pylint errors at this stage)
  $ git add -A
  $ git commit -m "[IMP] $MODULE: black, isort, prettier"  --no-verify  # it is important to do all formatting in one commit the first time
  ```
* Check https://github.com/OCA/maintainer-tools/wiki/Merge-commits-in-pull-requests for a procedure for reducing commits from "OCA Transbot...".
* Adapt the module to the 15.0 version following tasks to do.
* On a shell command, type this for uploading the content to GitHub:
  ```bash
  $ git add --all
  $ git commit -m "[MIG] $MODULE: Migration to 15.0"
  $ git remote add $USER_ORG git@github.com:$USER_ORG/$REPO.git # This mode requires an SSH key in the GitHub account
  $ ... or ....
  $ git remote add $USER_ORG https://github.com/$USER_ORG/$REPO.git # This will required to enter user/password each time
  $ git push $USER_ORG 15.0-mig-$MODULE --set-upstream
  ```
* Follow the link on the command line or check in https://docs.github.com/en/free-pro-team@latest/github/collaborating-with-issues-and-pull-requests/creating-a-pull-request-from-a-fork how to create the pull request.

**Troubleshooting**

Sometimes, when performing these operations, the process can hang due to conflicts on the patches being applied. One of the possible problems is because a patch removes entirely a file and `git am` is not able to resolve this. It ends with a message `error: ...: patch does not apply`.

If that's your case, you can add `--ignore-whitespace` at the end of the `git am` command for allowing the patch to be applied, although there will be still conflicts to be solved, but they will be doable through standard conflict resolution system. Starting from the previous situation, you will need to do:

```bash
git am --abort
git format-patch --keep-subject --stdout origin/15.0..origin/14.0 -- <module path> | git am -3 --keep --ignore-whitespace
# resolve conflicts (for example git rm <file>)
git add --all
git am --continue
```
