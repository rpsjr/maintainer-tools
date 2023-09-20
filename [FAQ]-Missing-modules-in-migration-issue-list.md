If a module is not listed in the corresponding migration issue list, but you are seeing it migrated in the previous version, it can be due to the module arrived on the previous branch after the current version branches were created, which is the moment when the issue is created and populated with currently existing modules.

So that doesn't mean the module is not needed in new version. This has to be checked by any contributor. If finally not needed, then the line for the module should be on the migration issue, but it should be crossed out by any of the maintainers, explaining the reason why it's not needed.

If you plan to migrate the module, you can write as always on the issue about that, and when doing the PR, a PSC or module maintainer can trigger the bot command to add the module to the list.
