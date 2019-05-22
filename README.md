# Dlint

Dlint is a tool for encouraging best coding practices and helping ensure we're
writing secure code.

> The most important thing I have done as a programmer in recent years is to
> aggressively pursue static code analysis. Even more valuable than the
> hundreds of serious bugs I have prevented with it is the change in mindset
> about the way I view software reliability and code quality.
> - [John Carmack, 2011](https://www.gamasutra.com/view/news/128836/InDepth_Static_Code_Analysis.php)

> For a static analysis project to succeed, developers must feel they benefit
> from and enjoy using it.
> - [Lessons from Building Static Analysis Tools at Google](https://cacm.acm.org/magazines/2018/4/226371-lessons-from-building-static-analysis-tools-at-google/fulltext)

# Installing

**TODO: Update with PyPI instructions**

And double check that it was installed correctly:

```
$ python -m flake8 -h
Usage: flake8 [options] file file ...

...

Installed plugins: dlint: 0.5.0, mccabe: 0.5.3, pycodestyle: 2.2.0, pyflakes: 1.3.0
```

Note the `dlint: 0.5.0`.

# Using

Dlint uses `flake8` to perform its linting functionality. This allows us to
utilize many useful `flake8` features without re-inventing the wheel.

## CLI

Let's run a simple check:

```
$ cat test.py
#!/usr/bin/env python

print("TEST1")

exec('print("TEST2")')
```

```
$ python -m flake8 --select=DUO test.py
test.py:5:1: DUO105 use of "exec" not allowed
```

The `--select=DUO` flag tells `flake8` to only run Dlint lint rules.

From here, we can run Dlint against a directory of Python code and make use
of all the functionality provided to us by `flake8`:

```
$ python -m flake8 --select=DUO /path/to/python/directory
```

## Arc Lint

Dlint is easily integrated with [Arcanist's](https://secure.phabricator.com/book/phabricator/article/arcanist/)
linting process via the [.arclint](https://secure.phabricator.com/book/phabricator/article/arcanist_lint/)
configuration file. Dlint rules will automatically be run via `flake8` once
it's installed, so the standard `flake8` configuration will work:

```
{
    "linters": {
        "sample": {
            "type": "flake8"
        }
    }
}
```

You can also utilize more granular control over the linting process:

```
{
    "linters": {
        "sample": {
            "type": "flake8"
        },
        "bin": ["python2.7", "python2"],
        "flags": ["-m", "flake8", "--select", "DUO"]
    }
}
```

## Inline Editor

Dlint results can also be included inline in your editor for fast feedback.
This typically requires an editor plugin or extension. Here are some starting
points for common editors:

* Vim: [https://github.com/vim-syntastic/syntastic](https://github.com/vim-syntastic/syntastic)
* Emacs: [https://github.com/flycheck/flycheck](https://github.com/flycheck/flycheck)
* Sublime: [https://github.com/SublimeLinter/SublimeLinter-flake8](https://github.com/SublimeLinter/SublimeLinter-flake8)
* PyCharm: [https://foxmask.net/post/2016/02/17/pycharm-running-flake8/](https://foxmask.net/post/2016/02/17/pycharm-running-flake8/)
* Atom: [https://atom.io/packages/linter-flake8](https://atom.io/packages/linter-flake8)
* Visual Studio Code: [https://code.visualstudio.com/docs/python/linting#_flake8](https://code.visualstudio.com/docs/python/linting#_flake8)

# Custom Plugins

Dlint's custom plugins are built on a [simple naming convention](https://packaging.python.org/guides/creating-and-discovering-plugins/#using-naming-convention),
and rely on [Python modules](https://docs.python.org/3/distutils/examples.html#pure-python-distribution-by-module).
To make a Dlint custom plugin use the following conventions:

* The Python module name **must** start with `dlint_plugin_`.
* The linter class name **must** start with `Dlint`.
* The linter class **should** inherit from `dlint.linters.base.BaseLinter`.
  * If for some reason you'd like to avoid this, then you **must** implement
	the `get_results` function appropriately and inherit from `ast.NodeVisitor`.

See an [example plugin](https://github.com/duo-labs/dlint-plugin-example) for further details.

# Developing

First, install development packages:

```
$ pip install -r requirements.txt
$ pip install -r requirements-dev.txt
$ pip install -e .
```

## Testing

```
$ pytest
```

## Linting

```
$ flake8
```

## Coverage

```
$ pytest --cov
```