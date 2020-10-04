# Before migrating

* Update yourself with the latest OCA Conventions: https://odoo-community.org/page/contributing
* Subscribe to the mailing list of the related project: https://odoo-community.org/groups
* Announce on the corresponding GitHub issue with the name "Migration to version 13.0" which module(s) you want to migrate

# Tasks to do in the migration

* Bump module version to `13.0.1.0.0`.
* Remove any possible migration script from previous version.
* Squash administrative commits (if any) with the previous commit for reducing commit noise. They are named as "[UPD] README.rst", "[UPD] Update $MODULE.pot", "Update translation files" and similar names, and comes from *OCA-git-bot*, *oca-travis* or *oca-transbot*. **IMPORTANT**: Don't squash legit translation commits, authored by their translators, with the message "Translated using Weblate (...)".
* Remove all the decorators `@api.multi`, `@api.returns`, `@api.one`, `@api.cr`, `@api.model_cr` from the code. Now they are all multi-record by default. In case of the last ones, you will need to adapt the code to the behavior change. 
* Check that all "compute" methods of non-stored computed fields assign a value in any case to the field, even if it is a falsy one.
* Computed stored fields will keep their previous value if not assigned during the compute method, so don't rely on any expected default value.
* Multi-company record rules now need to have this general form: `['|',('company_id','=',False),('company_id','in',company_ids)]</field>`, and you need to add migration script as well when being noupdate="1" record (which should be the majority). See https://github.com/OCA/credit-control/pull/54/commits/162cde23fe5c0dd12f7de1fa01d36a0bc156008e for an example.
* For the rest of multi-company considerations, see https://www.odoo.com/documentation/13.0/howtos/company.html.
* Replace `sudo(user)` (but keep `sudo()`). Use `with_user(user)` instead.
* Change `track_visibility="..."` field attribute by `tracking=True`.
* Some of the Font Awesome (FA) icons have changed their name as now Odoo uses FA v5, so you might need to change them in your module views. Check the changed names in https://fontawesome.com/how-to-use/on-the-web/setup/upgrading-from-version-4#name-changes.
* Remove all the `oldname` field attributes in the code. If they were added in previous version, they have served their function any way, and now in this version it's not supported, so if you have the need, create a migration script and use openupgradelib's `rename_fields` method.
* Remove `view_type` tag on action window XML definition. It's now always `form` (`tree` is not supported since 11.0 any way).
* Remove `multi` field from `ir.actions.act_window` models. Now you have `binding_view_types` field for indicating in which view the action will be available: `list`, `form` or empty for both. If declaring the action through the accelerator tag `<act_window>`, then use the attribute `binding_views`. More reference in https://github.com/odoo/odoo/pull/24738/commits/33d51480688065e367eb646f12b89d721749cac9.
* If having an smart-button for `active` field, with widget `toggle_button` / `boolean_button`, the archive/unarchive actions are available without doing anything more, so you can remove it. And the new paradigm is to put instead a ribbon when archived with the code:

  ```xml
  <widget name="web_ribbon" text="Archived" bg_color="bg-danger" attrs="{'invisible': [('active', '=', True)]}"/>
  <field name="active" invisible="1" />
  ```

* If using any decimal precision in float fields (example: `import odoo.addons.decimal_precision as dp; x = fields.Float(digits=dp.get_precision("Account"))`), now the qualifier is put directly without the need of importing anything and simplifying syntax: `x = fields.Float(digits="Account")`.
* In the manifest, rename python dependencies to use the PyPI distribution name instead of the import name (see https://github.com/odoo/odoo/pull/25549 for more information)
* In JavaScript, replace jQuery promises (`$`) by native `Promise` (example: `$.when()` becomes `Promise.resolve()`).
* In JavaScript, replace `config.debug` sentence for getting if in debug mode by `config.isDebug()`.
* If the module is touching Accounting part, see https://github.com/OCA/maintainer-tools/issues/430 for the structural changes detected in it.
* Add tests to increase code coverage.
* Check tasks of previous versions if you are migrating from lower versions than v12. It's also recommended to check for things not done in previous migrations.
* Do the rest of the changes you need to do for making the module works on new version.

Regex which can help to find the things to remove/change:

```
grep -nri 'oldname\|sudo([^\)]\+)\|api.multi\|api.returns\|api.one\|api.cr\|api.model_cr\|12.0\|compute=' $MODULE
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
  $ pre-commit run -a  # to run black, isort and prettier (ignore pylint errors at this stage)
  $ git add -A
  $ git commit -m "[IMP] $MODULE: black, isort, prettier"  --no-verify  # it is important to do all formatting in one commit the first time
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
$ pre-commit run -a  # to runs black, isort and prettier (ignore pylint errors at this stage)
$ git add -A
$ git commit -m "[IMP] $MODULE: black, isort, prettier" --no-verify  # it is important to do all formatting in one commit the first time
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
