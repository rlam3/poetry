---
draft: false
layout: single
menu:
  docs:
    weight: 11
title: Managing dependencies
type: docs
---


# Managing dependencies

## Dependency groups

Poetry provides a way to **organize** your dependencies by **groups**. For instance, you might have
dependencies that are only needed to test you project or to build the documentation.

To declare a new dependency group, use a `tool.poetry.group.<group>` section
where `<group>` is the name of your dependency group (for instance, `test`):

```toml
[tool.poetry.group.test]  # This part can be left out

[tool.poetry.group.test.dependencies]
pytest = "^6.0.0"
pytest-mock = "*"
```

{{% note %}}
All dependencies **must be compatible with each other** across groups since they will
be resolved regardless of whether they are required for installation or not (see [Installing group dependencies]({{< relref "#installing-group-dependencies" >}})).
{{% /note %}}

The dependencies declared in `tool.poetry.dependencies` are part of an implicit `default` group.

```toml
[tool.poetry.dependencies]  # Default dependency group
httpx = "*"
pendulum = "*"

[tool.poetry.group.test.dependencies]
pytest = "^6.0.0"
pytest-mock = "*"
```

{{% note %}}
**A note about the `dev-dependencies` section**

Any dependency declared in the `dev-dependencies` section will automatically be added to a `dev` group.
So the two following notations are equivalent:

```toml
[tool.poetry.dev-dependencies]
pytest = "^6.0.0"
pytest-mock = "*"
```

```toml
[tool.poetry.group.dev.dependencies]
pytest = "^6.0.0"
pytest-mock = "*"
```

Poetry will slowly transition away from the `dev-dependencies` notation which will soon be deprecated,
so it's advised to migrate your existing development dependencies to the new `group` notation.
{{% /note %}}

### Optional groups

A dependency group can be declared as optional. This makes sense when you have
a group of dependencies that are only required in a particular environment or for
a specific purpose.

```toml
[tool.poetry.group.docs]
optional = true

[tool.poetry.group.docs.dependencies]
mkdocs = "*"
```

{{% warning %}}
Optional group dependencies will **still** be resolved along other dependencies, so
special care should be taken to ensure they are compatible with each other.
{{% /warning %}}

### Adding a dependency to a group

The [`add`]({{< relref "cli#add" >}}) command is the preferred way to add dependencies
to a group. This is done by using the `--group (-G)` option.

```bash
poetry add pytest --group test
```

If the group does not already exist, it will be created automatically.

### Installing group dependencies

**By default**, dependencies across **all groups** will be installed when executing `poetry install`.

You can **exclude** one or more groups with the `--without` option:

```bash
poetry install --without test,docs
```

You can also opt in [optional groups]({{< relref "#optional-groups" >}}) by using the `--with` option:

```bash
poetry install --with docs
```

If you only want to install the **default**, non-grouped, dependencies, you can do so
with the `--default` option:

```bash
poetry install --default
```

Finally, in some case you might want to install **only specific groups** of dependencies
without installing the default dependencies. For that purpose, you can use
the `--only` option.

```bash
poetry install --only docs
```
