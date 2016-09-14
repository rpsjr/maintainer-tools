# Before migrating

* Update yourself with the latest OCA Conventions: https://github.com/OCA/maintainer-tools/blob/master/CONTRIBUTING.md
* Subscribe to the mailing list of the related project: https://odoo-community.org/groups
* Announce on the mailing list which modules you want to migrate

# Todo

* Update README.rst from https://raw.githubusercontent.com/OCA/maintainer-tools/master/template/module/README.rst
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

### If the module doesn't exist in the 9.0 branch:

```shell
# Download repository
git clone git@github.com:OCA/<repo>.git -b 8.0
cd <repo>
# Create branch for isolating the module
git checkout -b 8.0-extract
# Filter commits and rewrite history for only the selected module
git filter-branch --subdirectory-filter <module_name>
git filter-branch -f --tree-filter 'mkdir -v <module_name> ; git mv -k * <module_name>' HEAD
# Go to 9.0 and merge filtered branch
git checkout 9.0
git checkout -b 9.0-<module>
git merge 8.0-extract
# Push to your repo
git remote add <user/org> git@github.com:<user/org>/<repo>.git
git push <user/org> 9.0-<module> --set-upstream
```

### If the module already exists in the 9.0 branch, but it doesn't contain all the commit history (or you're not sure if it have it):

```shell
# Download repository
git clone git@github.com:OCA/<repo>.git -b 8.0
cd <repo>
# Create branch for isolating the module
git checkout -b 8.0-extract
# Filter commits and rewrite history for only the selected module
git filter-branch --subdirectory-filter <module_name>
git filter-branch -f --tree-filter 'mkdir -v <module_name> ; git mv -k * <module_name>' HEAD
# Go to 9.0 and apply only filtered commits from the migration date
git checkout 9.0
git checkout -b 9.0-<module>
git log refs/heads/8.0-extract --since=2015-10-14 --pretty=format:'%H' --reverse | while read line || [ -n "$line" ] ; do
  git cherry-pick $line
done
# Push to your repo
git remote add <user/org> git@github.com:<user/org>/<repo>.git
git push <user/org> 9.0-<module> --set-upstream
```

### Experimental procedure that works with merge commits and can be restarted in case of conflicts

- First clone the repository.
- Checkout the 8.0 branch: `git checkout 8.0`.
- Checkout the 9.0 branch: `git checkout 9.0`
- Create a new branch for your migration: `git checkout -b 9.0-mig-<module_name>`
- Run the script below
- If it stops with an error (most probably a conflict or an empty merge commit), resolve the error and follow git's instructions (such as `git am --continue` or `git am --skip` for empty merges). 
- Then restart the script to process the next commits, until all succeed.

```bash
#!/bin/bash
SOURCE_BRANCH=8.0
TARGET_BRANCH=9.0
SOURCE_PATH=<module_name>
DONE_FILE=/tmp/mig-done-$SOURCE_PATH

set -e
# for each commit on SOURCE_BRANCH since the common ancestor of SOURCE and TARGET which touches SOURCE_PATH
for sha in $(git log --oneline --reverse --format=%H $(git merge-base $SOURCE_BRANCH $TARGET_BRANCH)..$SOURCE_BRANCH $SOURCE_PATH)
do
    if grep -Fxq $sha $DONE_FILE
    then
        echo skipping $sha already in $DONE_FILE
    else
        echo applying $sha
        echo $sha >> $DONE_FILE
        git log -p --format=email -1 $sha -- $SOURCE_PATH | git am -3
    fi
done
```

# Initialization (already done in OCA)

Before migrating the first module, the following tasks must be performed:

* Create 9.0 branch from 8.0
* Delete `__unported__` directory
* Update all modules manifest with installable = False
* Update metafiles (.travis.yml and README.md), adapting them from the template in https://github.com/OCA/maintainer-quality-tools/tree/master/sample_files to have the proper data.
* Make 9.0 default branch
* Remove master branch if it still exists