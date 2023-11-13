# Before migrating

* Update yourself with the latest OCA Conventions: https://odoo-community.org/page/contributing
* Subscribe to the mailing list of the related project: https://odoo-community.org/groups
* On the corresponding GitHub issue named "Migration to version 17.0", announce which module(s) you want to migrate
* [[Install pre-commit]]

# Tasks to do in the migration

* Bump module version to `17.0.1.0.0`.
* Remove any possible migration script from previous version (in a nutshell, remove `migrations` folder inside the module if exists).
* Squash administrative commits (if any) with the previous commit for reducing commit noise. Check https://github.com/OCA/maintainer-tools/wiki/Merge-commits-in-pull-requests#mergesquash-the-commits-generated-by-bots-or-weblate for details.
* If you are overriding `name_get` method, you should now override `_compute_display_name` instead, adding the possible new fields in the `depends` of the method. See https://github.com/odoo/odoo/pull/122085 for more details.
* If you are using any module hook (pre_init_hook, post_init_hook or uninstall_hook), the argument that is now passed is `env`, so you should convert your methods. See https://github.com/odoo/odoo/commit/b4a7996e967621aa090dc80346e6c3ef1d032dcf for more details.
* Replace the usage of `get_resource_path` by `file_path` with the new argument syntax. Some examples in https://github.com/odoo/odoo/pull/135607.
* If you are using `active_id`, `active_ids` or `active_model` in your views (for example, for a smart-button context), you should replace them by the corresponding good practices:
  * `id` instead of `active_id`.
  * Hard-coded model name instead of `active_model`.
  * `active_ids` doesn't have direct replacement candidate. It will depend on what you want to achieve. It's a weird case though.

  A warning will be issued if not replaced. Check the framework change and replacement examples at https://github.com/odoo/odoo/commit/daf05d48ac76d500aa1285184bdddc4c67641d58.
* Remove `owl="1"` from OWL templates, as it's not needed anymore as all of them are OWL now. Reference: https://github.com/odoo/odoo/pull/130467. Application of this removal in Odoo core: https://github.com/odoo/odoo/pull/141383.
* Add tests to increase code coverage.
* Check tasks of previous versions if you are migrating from lower versions than v16. It's also recommended to check past migration guides for things not done in previous migrations.
* Do the rest of the changes you need to do for making the module works on new version.

# Tasks NOT to do in the migration

* change copyright year

  A line like this:

  ```
  # Copyright 2017 ACME Ltd - Johnny Glamour
  ```
  says "copyright FROM 2017". There's no need to change the year to the current one.

* change original authors

# How-to

## Technical method to migrate a module from "16.0" to "17.0" branch

* `$repo`: the OCA repository hosting the module
* `$module`: the name of the module you want to migrate
* `$user_org`: your GitHub login or organization name

**Full process**

* On a shell command:
  ```bash
  $ git clone https://github.com/OCA/$repo -b 17.0
  $ cd $repo
  $ git checkout -b 17.0-mig-$module origin/17.0
  $ git format-patch --keep-subject --stdout origin/17.0..origin/16.0 -- $module | git am -3 --keep
  $ pre-commit run -a  # to run pre-commit linters and formatters (please ignore pylint errors at this stage)
  $ git add -A
  $ git commit -m "[IMP] $module: pre-commit auto fixes"  --no-verify  # it is important to do all formatting in one commit the first time
  ```
* Check https://github.com/OCA/maintainer-tools/wiki/Merge-commits-in-pull-requests for a procedure for reducing commits from "OCA Transbot...".
* Adapt the module to the 17.0 version following tasks to do.
* On a shell command, type this for uploading the content to GitHub:
  ```bash
  $ git add --all
  $ git commit -m "[MIG] $module: Migration to 17.0"
  $ git remote add $user_org git@github.com:$user_org/$repo.git # This mode requires an SSH key in the GitHub account
  $ ... or ....
  $ git remote add $user_org https://github.com/$user_org/$repo.git # This will required to enter user/password each time
  $ git push $user_org 17.0-mig-$module --set-upstream
  ```
* Follow the link on the command line or check in https://docs.github.com/en/free-pro-team@latest/github/collaborating-with-issues-and-pull-requests/creating-a-pull-request-from-a-fork how to create the pull request.
* For being easily searchable and avoiding to duplicate efforts, please name the pull request following this pattern: `[17.0][MIG] <module>: Migration to 17.0`.

**Troubleshooting**

Sometimes, when performing these operations, the process can hang due to conflicts on the patches being applied. One of the possible problems is because a patch removes entirely a file and `git am` is not able to resolve this. It ends with a message `error: ...: patch does not apply`.

If that's your case, you can add `--ignore-whitespace` at the end of the `git am` command for allowing the patch to be applied, although there will be still conflicts to be solved, but they will be doable through standard conflict resolution system. Starting from the previous situation, you will need to do:

```bash
git am --abort
git format-patch --keep-subject --stdout origin/17.0..origin/16.0 -- <module path> | git am -3 --keep --ignore-whitespace
# resolve conflicts (for example git rm <file>)
git add --all
git am --continue
```
