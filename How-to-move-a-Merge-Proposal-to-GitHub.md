# How to move a Merge Proposal to GitHub

Situation: for example, I have this MP that I need to propose on GitHub:
https://code.launchpad.net/~camptocamp/openerp-connector-magento/7.0-update-stock-1330450/+merge/223393

## Using `git-remote-bzr`

Method using only Git and `git-remote-bzr`.

### Install the tool

* **Instructions to install `git-remote-bzr` on** https://github.com/felipec/git-remote-bzr

### Fork the repo

I go on https://github.com/OCA/connector-magento and I Fork the repo in my account, so I have https://github.com/guewen/connector-magento.

I clone the repository:

    $ git clone git@github.com:guewen/connector-magento.git

I add the remote from the `bzr` branch:

    $ cd connector-magento
    $ git remote add 7.0-update-stock-1330450 bzr::lp:~camptocamp/openerp-connector-magento/7.0-update-stock-1330450
    $ git fetch 7.0-update-stock-1330450

I push the remote branch to GitHub

    $ git push origin refs/remotes/7.0-update-stock-1330450/master:refs/head/7.0-update-stock-1330450 

My branch is now in GitHub

### Open the Pull Request

I open the new branch https://github.com/guewen/connector-magento/tree/7.0-update-stock-1330450
and create the pull request. I put the link to the original Launchpad request for the reviewers and committers.

Result here: https://github.com/OCA/connector-magento/pull/1

## Using `oca-copy-branches`

This may be more convenient if you need to copy several branches at once.

### Install the tool

* **Instructions to install `git-remote-bzr` on** https://github.com/felipec/git-remote-bzr
* **Instructions to install `oca-copy-branches` on** https://github.com/OCA/maintainers-tools#installation

### Fork the repo

I go on https://github.com/OCA/connector-magento and I Fork the repo in my account, so I have https://github.com/guewen/connector-magento.

### Create a mapping file (in `yaml` format)

On this model:

    [mapping.yml]

    projects:
      - github: git@github.com:guewen/connector-magento.git
        branches:
        - ['lp:~camptocamp/openerp-connector-magento/7.0-update-stock-1330450', 7.0-update-stock-1330450]

`github` is the GitHub repository where will the branch be pushed
`branches` is a list of branches to move from Launchpad to the GitHub repository, each line contains 1. the Launchpad URL of the branch, 2. the name that will be given to the branch in the Git repository

You can declare several projects or branches at once.

### Run the command
  
    $ mkdir branches
    $ oca-copy-branches branches --push --mapping mapping.yml

The branch(es) will be moved to GitHub.

### Open the Pull Request

I open the new branch https://github.com/guewen/connector-magento/tree/7.0-update-stock-1330450
and create the pull request. I put the link to the original Launchpad request for the reviewers and committers.

Result here: https://github.com/OCA/connector-magento/pull/1