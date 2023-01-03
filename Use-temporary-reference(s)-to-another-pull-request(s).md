Due to the modular nature of Odoo and the OCA flows requiring peer reviews, when you are migrating or developing modules, you may have several pull requests opened that depends on another.

For being able to both test them on the quality checks and review them functionally on runboat, you can use the pip requirements format for installing the dependency directly from such pull request.

For that, you need to create (it not exists yet) or edit the file `test-requirements.txt` on the root folder of the OCA repository. That file is an OCA convention for using such requirements for the tests and runboat, on contrast to the file `requirements.txt`, that can be used (and it's automatically filled from the external dependencies) for preparing the deployment environment.

On it, you can add a line for each pull request with this format:

**For <=v14:**

```
odoo<series>-addon-<module_name> @ git+https://github.com/OCA/<repository>.git/refs/pull/<PR_number>/head#subdirectory=setup/<module_name>
```

Example:

```
odoo13-addon-survey_sale_generation @ git+https://github.com/OCA/survey.git@refs/pull/65/head#subdirectory=setup/survey_sale_generation
```

**For >=v15:**

```
odoo-addon-<module_name> @ git+https://github.com/OCA/<repository>.git/refs/pull/<PR_number>/head#subdirectory=setup/<module_name>
```

Example:

```
odoo-addon-product_packaging_level @ git+https://github.com/OCA/product-attribute.git@refs/pull/1215/head#subdirectory=setup/product_packaging_level
```

Please put such changes on a separate commit with a commit message similar to this one:

```
[DON'T MERGE] test-requirements.txt
```

so that you will be able to simply remove such commit when the pull request is merged. There's a linter in the CI for avoiding to merge pull requests that contain such temporary references.

*NOTE*: This is only valid for branches with the PIP install mode, which should be most of the 13.0 ones, and all of the 14.0+.