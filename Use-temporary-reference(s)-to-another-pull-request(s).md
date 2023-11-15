Due to the modular nature of Odoo and the OCA flows requiring peer reviews, when you are migrating or developing modules, you may have several pull requests opened that depends on another.

For being able to both test them on the quality checks and review them functionally on runboat, you can use the pip requirements format for installing the dependency directly from such pull request.

For that, you need to create (if not exists yet) or edit the file `test-requirements.txt` on the root folder of the OCA repository. That file is an OCA convention for using such requirements for the tests and runboat, on contrast to the file `requirements.txt`, that can be used (and it's automatically filled from the external dependencies) for preparing the deployment environment.

On it, you can add a line for each pull request with this format:

**For <=v14:**

```
odoo<series>-addon-<module_name> @ git+https://github.com/OCA/<repository>.git@refs/pull/<PR_number>/head#subdirectory=setup/<module_name>
```

Example:

```
odoo13-addon-survey_sale_generation @ git+https://github.com/OCA/survey.git@refs/pull/65/head#subdirectory=setup/survey_sale_generation
```

**For v15 and v16:**

Starting with Odoo 15, we use python versions that have the modern pip resolver, so we don't need to encode the Odoo series in the addon name anymore:

```
odoo-addon-<module_name> @ git+https://github.com/OCA/<repository>.git@refs/pull/<PR_number>/head#subdirectory=setup/<module_name>
```

Example:

```
odoo-addon-product_packaging_level @ git+https://github.com/OCA/product-attribute.git@refs/pull/1215/head#subdirectory=setup/product_packaging_level
```

**For >=v17:**

Starting from Odoo 17, the python project is not in the `setup/` directory but each addon is a little python project in itself (it has a pyproject.toml in it), so the subdirectory fragment is the addon name.

```
odoo-addon-<module_name> @ git+https://github.com/OCA/<repository>.git@refs/pull/<PR_number>/head#subdirectory=<module_name>
```

Example:

```
odoo-addon-product_packaging_level @ git+https://github.com/OCA/product-attribute.git@refs/pull/1215/head#subdirectory=product_packaging_level
```

Please put such changes on a separate commit with a commit message similar to this one:

```
[DON'T MERGE] test-requirements.txt
```

so that you will be able to simply remove such commit when the pull request is merged.

**WARNING**: There's a linter in the CI for avoiding to merge pull requests that contain such temporary references, so you will have a temporary ❌ on the checks, but the rest of them will use the referenced PR and should be green (except codecov ones, that are allowed to fail, but recommended to be improved).

**NOTE**: This is only valid for branches with the PIP install mode, which should be most of the 13.0 ones, and all of the 14.0+.

**NOTE 2**: It's convenient to mention on the pull request that it depends on the other one with this format:

```
Depends on:

- [ ] <url_of_the_dependent_pr>
```

This way, GitHub references both of them, and track if the dependent one is merged.