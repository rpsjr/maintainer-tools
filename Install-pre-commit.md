# Introduction

[pre-commit](https://pre-commit.com/) is a tool used in OCA that allows to trigger automatically some checks and changes when committing in git.

Some of the hooks we have configured in OCA repositories:

- ruff formatting and checking tool (flake8, black, autoflake and isort for older versions)
- pylint, pylint-odoo and pre-commit checks
- setuptools-odoo folder creation
- prettier for XML
- eslint for JS
- ...

# Installation

To install pre-commit, you can use the most used package installer for Python `pip`, and simply type in your command line:

```
pip install pre-commit
```

Refer to your distribution documentation to see how to install `pip` (if not already installed).

But for isolating dependencies, it's better to use another package installer called [pipx](https://github.com/pypa/pipx#install-pipx). The installation command line will be this time:

```
pipx install pre-commit
```

# Repository preparation

It's good to have pre-commit stuff auto-launched when running `git commit`. To get that, you can type on each repository `pre-commit install -f`. New cloned and created repositories will have it by default.

# Manual run

You can run pre-commit anytime launching this command inside the repository folder:

```
pre-commit run -a
```