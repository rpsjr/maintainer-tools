# Before migrating

* Update yourself with the latest OCA Conventions: https://odoo-community.org/page/contributing
* Subscribe to the mailing list of the related project: https://odoo-community.org/groups
* On the corresponding GitHub issue named "Migration to version 16.0", announce which module(s) you want to migrate

# Tasks to do in the migration

* Bump module version to `16.0.1.0.0`.
* Remove any possible migration script from previous version (in a nutshell, remove `migrations` folder inside the module if exists).
* Squash administrative commits (if any) with the previous commit for reducing commit noise. They are named as "[UPD] README.rst", "[UPD] Update $MODULE.pot", "Update translation files" and similar names, and comes from *OCA-git-bot*, *oca-travis* or *oca-transbot*. **IMPORTANT**: Don't squash legit translation commits, authored by their translators, with the message "Translated using Weblate (...)".
* If you are overriding `name_search` method in your module, you may make use now of new `_rec_names_search` class variable to expose the fields to search for without requiring the method override. More details at https://github.com/odoo/odoo/commit/3155c3e425581b71491844e7f9a3dd76a9f245a4.
* Any view with `groups_id` on it now has to move such groups to the elements of the view. But now you can put `groups` attribute in a view field instead of isolating it on a view without fear of an access error. Check both things in https://github.com/odoo/odoo/pull/98551 and https://github.com/odoo/odoo/pull/95729.
* If using `fields_get_keys()` method for getting fields definition, now use directly `_fields` variable or `get_views()` method.
* If using `get_xml_id()` method for getting an ID, now use the method `get_external_id()`.
* The methods `flush()` and `recompute()` are deprecated. Use `flush_model()`, `flush_recordset()` or `env.flush_all()` instead depending on the needed granularity.
* The methods `refresh()` and `invalidate_cache()` are deprecated. Use `invalidate_model()`, `invalidate_recordset()` or `env.invalidate_all()` instead depending on the needed granularity.
* Add tests to increase code coverage.
* Check tasks of previous versions if you are migrating from lower versions than v15. It's also recommended to check past migration guides for things not done in previous migrations.
* Do the rest of the changes you need to do for making the module works on new version.

# How-to

## Technical method to migrate a module from "15.0" to "16.0" branch

* `$repo`: the OCA repository hosting the module
* `$module`: the name of the module you want to migrate
* `$user_org`: your GitHub login or organization name

**Full process**

* On a shell command:
  ```bash
  $ git clone https://github.com/OCA/$repo -b 16.0
  $ cd $repo
  $ git checkout -b 16.0-mig-$module origin/16.0
  $ git format-patch --keep-subject --stdout origin/16.0..origin/15.0 -- $module | git am -3 --keep
  $ pre-commit run -a  # to run black, isort and prettier (ignore pylint errors at this stage)
  $ git add -A
  $ git commit -m "[IMP] $module: pre-commit stuff"  --no-verify  # it is important to do all formatting in one commit the first time
  ```
* Check https://github.com/OCA/maintainer-tools/wiki/Merge-commits-in-pull-requests for a procedure for reducing commits from "OCA Transbot...".
* Adapt the module to the 16.0 version following tasks to do.
* On a shell command, type this for uploading the content to GitHub:
  ```bash
  $ git add --all
  $ git commit -m "[MIG] $module: Migration to 16.0"
  $ git remote add $user_org git@github.com:$user_org/$repo.git # This mode requires an SSH key in the GitHub account
  $ ... or ....
  $ git remote add $user_org https://github.com/$user_org/$repo.git # This will required to enter user/password each time
  $ git push $user_org 16.0-mig-$module --set-upstream
  ```
* Follow the link on the command line or check in https://docs.github.com/en/free-pro-team@latest/github/collaborating-with-issues-and-pull-requests/creating-a-pull-request-from-a-fork how to create the pull request.

**Troubleshooting**

Sometimes, when performing these operations, the process can hang due to conflicts on the patches being applied. One of the possible problems is because a patch removes entirely a file and `git am` is not able to resolve this. It ends with a message `error: ...: patch does not apply`.

If that's your case, you can add `--ignore-whitespace` at the end of the `git am` command for allowing the patch to be applied, although there will be still conflicts to be solved, but they will be doable through standard conflict resolution system. Starting from the previous situation, you will need to do:

```bash
git am --abort
git format-patch --keep-subject --stdout origin/16.0..origin/15.0 -- <module path> | git am -3 --keep --ignore-whitespace
# resolve conflicts (for example git rm <file>)
git add --all
git am --continue
```
