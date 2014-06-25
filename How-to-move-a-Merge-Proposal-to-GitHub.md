# How to move a Merge Proposal to GitHub

I have this MP that I need to propose on GitHub:
https://code.launchpad.net/~camptocamp/openerp-connector-magento/7.0-update-stock-1330450/+merge/223393

## Install the tool

**Instructions on** https://github.com/OCA/maintainers-tools#installation

## Fork the repo

I go on https://github.com/OCA/connector-magento and I Fork the repo in my account, so I have https://github.com/guewen/connector-magento.

## Create a mapping file (in `yaml` format)

On this model:

    [mapping.yml]

    projects:
      - github: git@github.com:guewen/connector-magento.git
        branches:
        - ['lp:~camptocamp/openerp-connector-magento/7.0-update-stock-1330450', 7.0-update-stock-1330450]

`github` is the GitHub repository where will the branch be pushed
`branches` is a list of branches to move from Launchpad to the GitHub repository, each line contains 1. the Launchpad URL of the branch, 2. the name that will be given to the branch in the Git repository

You can declare several projects or branches at once.

## Run the command
  
    $ mkdir branches
    $ oca-copy-branches branches --push --mapping mapping.yml

The branch(es) will be moved to GitHub.

## Open the Pull Request

I open the new branch https://github.com/guewen/connector-magento/tree/7.0-update-stock-1330450
and create the pull request. I put the link to the original Launchpad request for the reviewers and committers.

Result here: https://github.com/OCA/connector-magento/pull/1