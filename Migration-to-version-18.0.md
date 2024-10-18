# Before migrating

* Update yourself with the latest OCA Conventions: https://odoo-community.org/page/contributing
* Subscribe to the mailing list of the related project: https://odoo-community.org/groups
* On the corresponding GitHub issue named "Migration to version 18.0", announce which module(s) you want to migrate
* [[Install pre-commit]]

# Tasks to do in the migration

**REMARK:** Here, we are highlighting **framework changes** from previous version to this one, not data-model changes, as they are very extensive, and the alternative may depends on the module requirements. You can check data-model changes for each core module in the `upgrade_analysis.txt` files inside OpenUpgrade project: https://github.com/OCA/OpenUpgrade.

**REMARK 2**: If you are doing a migration jumping several versions, please check all the tasks in the successive "Migration to version XX.0" guides until arriving to this one.

* Bump module version to `18.0.1.0.0`.
* Remove any possible migration script from previous version (in a nutshell, remove `migrations` folder inside the module if exists).
* Squash administrative commits (if any) with the previous commit for reducing commit noise. Check https://github.com/OCA/maintainer-tools/wiki/Merge-commits-in-pull-requests#mergesquash-the-commits-generated-by-bots-or-weblate for details.
* Replace `tree` view type by `list`. This means everywhere in Python, JS or XML where there's a mentioning of this type.

  There's a script that automates most of the work provided by Odoo itself. To use it, have a local minimal Odoo installation, and run:

  ```shell
  <path_to_odoo>/odoo-bin upgrade_code --addons-path <path_to_module>
  ```

  See this global change with examples in the Odoo PR: https://github.com/odoo/odoo/pull/159909.
* `user_has_groups` has been removed, so now you should use `self.env.user.has_group`. More details and examples of the replacement in https://github.com/odoo/odoo/pull/151597.
* Methods `check_access_rights()` and `check_access_rule()` are replaced by a single method `check_access()`, the same as `_filter_access_rule()` and `_filter_access_rule_python()` by `_filter_access()`. More info at https://github.com/odoo/odoo/pull/179148.
* `_name_search` is gone, replaced by `_search_display_name`
* be careful with searches on non stored related fields: they don't log a warning anymore, they throw an exception
* Add tests to increase code coverage.
* Check tasks of previous versions if you are migrating from lower versions than 17. It's also recommended to check past migration guides for things not done in previous migrations.
* Do the rest of the changes you need to perform for making the module works on the new version.

# Tasks NOT to do in the migration

* change copyright year

  A line like this:

  ```
  # Copyright 2024 ACME Ltd - Johnny Glamour
  ```
  says "copyright FROM 2024". There's no need to change the year to the current one.

* change original authors

# How-to

## Technical method to migrate a module from "17.0" to "18.0" branch

* `$repo`: the OCA repository hosting the module
* `$module`: the name of the module you want to migrate
* `$user_org`: your GitHub login or organization name

**Full process**

* On a shell command:
  ```bash
  $ git clone https://github.com/OCA/$repo -b 18.0
  $ cd $repo
  $ git checkout -b 18.0-mig-$module origin/18.0
  $ git format-patch --keep-subject --stdout origin/18.0..origin/17.0 -- $module | git am -3 --keep
  $ pre-commit run -a  # to run pre-commit linters and formatters (please ignore pylint errors at this stage)
  $ git add -A
  $ git commit -m "[IMP] $module: pre-commit auto fixes"  --no-verify  # it is important to do all the formatting in one commit the first time
  ```
* Check https://github.com/OCA/maintainer-tools/wiki/Merge-commits-in-pull-requests for a procedure for reducing commits from "OCA Transbot...".
* Adapt the module to the 18.0 version following tasks to do.
* On a shell command, type this for uploading the content to GitHub:
  ```bash
  $ git add --all
  $ git commit -m "[MIG] $module: Migration to 18.0"
  $ git remote add $user_org git@github.com:$user_org/$repo.git # This mode requires an SSH key in the GitHub account
  $ ... or ....
  $ git remote add $user_org https://github.com/$user_org/$repo.git # This will required to enter user/password each time
  $ git push $user_org 18.0-mig-$module --set-upstream
  ```
* Follow the link on the command line or check in https://docs.github.com/en/free-pro-team@latest/github/collaborating-with-issues-and-pull-requests/creating-a-pull-request-from-a-fork how to create the pull request.
* For being easily searchable and avoiding to duplicate efforts, please name the pull request following this pattern: `[18.0][MIG] <module>: Migration to 18.0`.

**Troubleshooting**

Sometimes, when performing these operations, the process can hang due to conflicts on the patches being applied. One of the possible problems is because a patch removes entirely a file and `git am` is not able to resolve this. It ends with a message `error: ...: patch does not apply`.

If that's your case, you can add `--ignore-whitespace` at the end of the `git am` command for allowing the patch to be applied, although there will be still conflicts to be solved, but they will be doable through standard conflict resolution system. Starting from the previous situation, you will need to do:

```bash
git am --abort
git format-patch --keep-subject --stdout origin/18.0..origin/17.0 -- <module path> | git am -3 --keep --ignore-whitespace
# resolve conflicts (for example git rm <file>)
git add --all
git am --continue
```
