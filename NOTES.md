# Notes

## 1. Creating your Sphinx project (15 minutes)

1. Introduction (5 minutes)
2. Recommend gitpod.io, which is free - GitHub Codespaces doesn't work for everybody
3. Open lumache.py and install the Python extension
4. Open a new terminal and start working there, upgrade pip

5. Let's add some additional requirements:

```
[project.optional-dependencies]
doc = [
    "sphinx~=4.3.0",
]
```

and `pip install -e .[doc]`.

6. `sphinx-quickstart docs`, separate build and source
7. Describe files but don't get to syntax yet
8. Split terminal, `make html`, launch `python3 -m http.server`, open browser, _voilà_!
9. Commit

## 2. MyST vs reStructuredText (5 minutes, or a bit less)

[TBD] Add slides or content about reST vs MyST

## 3. Write Markdown, build HTML (15 minutes)

1. Let's convert our documentation to MyST! `pip install rst-to-myst[sphinx]`
2. No need to change directory, `rst2myst convert docs/**/*.rst`
3. A nice `index.md` appears, we can compare side to side
4. Remove `index.rst`, we won't need it anymore
5. However, `make html` now fails
6. We need to add the `myst_parser` extension to `conf.py`
7. After which, we have to install it
8. Now `make html` works, and the result is exactly the same
9. Commit

10. Good moment to cancel the HTTP server and
    `pip install sphinx-autobuild && sphinx-autobuild docs/source docs/build/html/`
    on the splitted terminal
11. Let's include the `README.md`!

```{include} ../../README.md
:relative-images:
```

12. And add an admonition
13. Commit again

## 4. Customizing Sphinx (5 minutes)

1. Enable `sphinx.ext.duration`
2. Let's change the theme! `html_theme = "furo"`
3. We need to add it to the requirements as well
4. And now it works!

More extensions: https://sphinx-extensions.readthedocs.io/en/latest/

## 5. Adding cross references (15 minutes)

1. Create new `usage.md` document with these contents:

````md
# Usage

## Installation

To use Lumache, install it with pip:

```console
(.venv) $ pip install lumache
```
````

2. Inspect warning, it's not on any table of contents!
3. Add it to index toctree
4. Add cross-reference using

```
Check out {doc}`usage`.
```

There are several domains available.

(Forget about sphinx-hoverxref until the documentation is hosted on Read the Docs)

5. Enable `sphinx.ext.autosectionlabel` (no need to set `autosectionlabel_prefix_document = True`)
6. Add another cross-reference using

```
Check out {ref}`Installation`.
```

7. Commit

## 6. Integrating Jupyter notebooks (10 minutes)

1. Create `notebooks/tutorial.ipynb`
2. Install `pip install nbsphinx`
3. Enable `nbsphinx` in Sphinx extensions
4. Install `sudo apt-get install pandoc`
5. Add `notebooks/tutorial` to toctree
6. Commit

## 7. Documenting code automatically (15 minutes)

1. Let's add some content to `usage.md`:

````md
## Creating recipes

To retrieve a list of random ingredients,
you can use the ``lumache.get_random_ingredients()`` function:

```{eval-rst}
.. function:: lumache.get_random_ingredients(kind=None)

   Return a list of random ingredients as strings.

   :param kind: Optional "kind" of ingredients.
   :type kind: list[str] or None
   :return: The ingredients list.
   :rtype: list[str]
```
````

2. But, I don't need it! Enable `sphinx.ext.autodoc`
3. And replace by this:

````
```{eval-rst}
.. autofunction:: lumache.get_random_ingredients
```
````

4. Check that, editing the docstring, the documentation gets updated!
5. Add a cross-reference to the function! Using

```
{py:func}`lumache.get_random_ingredients`
```

6. Document the exception as well:

````
```{eval-rst}
.. autoexception:: lumache.InvalidKindError
```
````

and notice that a cross-reference is generated from the docstring!

7. Enable `sphinx.ext.intersphinx` and add this configuration:

```
intersphinx_mapping = {'python': ('https://docs.python.org/3', None)}
```

and add

```
{py:class}`str`
```

Magic!!

8. Commit

## 8. Deploying to Read the Docs (10 minutes)

1. Login to RTD, sync repositories
2. Import project
3. Check that a build corresponding to the `main` branch is made, which fails - because we didn't specify requirements!
4. Add this to `.readthedocs.yaml`:

```yaml
version: 2

build:
  os: "ubuntu-20.04"
  tools:
    python: "3.9"

sphinx:
  configuration: docs/source/conf.py

python:
  install:
    - method: pip
      path: .
      extra_requirements:
        - doc
```

5. Commit and push
6. Notice how the new build works!
