# Can we turn Elegant Scipy into a Jupyter-book?

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/mwouts/jupyter-book-experiments/master?filepath=Elegant_Scipy_as_a_Jupyter_Book.md)

This document is a Bash Jupyter notebook that you can run interactively in Jupyter - simply click on the Binder link above.

## Create an environment with latest Jupyter Book and Jupytext

Here I will assume that you are working in a conda or a virtual environment. You could have used, for instance

```
conda create -n jupytextbook -y
conda activate jupytextbook
```

In this environment we install the latest version of Jupytext

```bash
git clone https://github.com/mwouts/jupytext.git
pip install ./jupytext
```

And of Jupyter Book

```bash
git clone https://github.com/jupyter/jupyter-book.git
pip install ./jupyter-book
```

## Download Elegant-Scipy

```bash
git clone https://github.com/elegant-scipy/elegant-scipy.git
```

We also install the book requirements. We could have created a separate env for that, but for simplicity we use the same environment as above

```bash
pip install -r elegant-scipy/requirements.txt
python -m ipykernel install --name elegant-scipy-kernel --user
```

## Turn Elegant Scipy into a Jupyter Book

In this paragraph we try to reproduce the folder structure expected by Jupyter Book. Here we follow the minimal book example from [`jupyter_book/minimal`](https://github.com/jupyter/jupyter-book/tree/master/jupyter_book/minimal).

### Create the Table of Contents

```bash
mkdir elegant-scipy/_data
echo '# Say we start with the Acknowledgements
- title: Elegant Scipy
  url: /acknowledgements
  not_numbered: true

# External link
- title: GitHub repository
  url: https://github.com/jupyter/jupyter-book
  external: true
  not_numbered: true

# Adds a search bar link
- title: Search
  search: true

# Divider for meta-pages and content page
- divider: true

# An now the chapters
- title: Preface
  url: /preface
  not_numbered: true
- title: Quantile Normalization with NumPy and SciPy
  url: /ch2
  not_numbered: true
- title: Epilogue
  url: /epilogue
  not_numbered: true
' > elegant-scipy/_data/toc.yml
```

### Create the `_config.yml` file

```bash
echo '#######################################################################################
# Jekyll site settings
title: Elegant Scipy
author: Juan Nunez-Iglesias (@jni), Harriet Dashnow (@hdashnow), and Stéfan van der Walt (@stefanv)
email: YOUR EMAIL

baseurl: "/" # the subpath of your site, e.g. /blog. If there is no subpath for your site, use an empty string ""
url: "YOUR URL" # the base hostname & protocol for your site, e.g. http://example.com

textbook_logo_link        : https://jupyter.org/jupyter-book/  # A link for the logo.

#######################################################################################
# Interact link settings

# General interact settings
use_jupyterlab                   : false  # If 'true', interact links will use JupyterLab as the interface

# Jupyterhub link settings
use_jupyterhub_button            : false  # If 'true', display a button that will direct users to a JupyterHub (that you provide)

# Binder link settings
use_binder_button                : false  # If 'true', add a binder button for interactive links
binder_repo_org                  : "elegant-scipy"  # The username or organization that owns this repository
binder_repo_name                 : "elegant-scipy"  # The name of the repository on the web

#######################################################################################
# Jupyter Book settings
content_folder_name : "markdown"
' > elegant-scipy/_config.yml
```

### Copy the jupyter book scripts and templates

```bash
jupyter-book create samplebook
cp -R samplebook/scripts elegant-scipy/
rm -rf samplebook
```

### Fix the notebook configuration

The notebooks seem to expect the `data` and `style` folders at the same level. So we add a few symbolic links:

```bash
cd elegant-scipy/markdown
ln -s ../data/ data
ln -s ../style/ style
cd ../..
```

### Change the extension to either md or ipynb

```bash
# Convert all notebooks to ipynb/md 
# ** ?? --execute not working on ch1.markdown ??
cd elegant-scipy/markdown
jupytext --to ipynb --set-kernel elegant-scipy-kernel --execute ch2.markdown

# ** ?? .markdown extension is not accepted by JupyterBook ?
mv acknowledgements.markdown acknowledgements.md
mv preface.markdown preface.md
mv epilogue.markdown epilogue.md
cd ../..
```

## Compile our book

Finally, we build the book with

```bash
jupyter-book build elegant-scipy
```

The intermediate result is a collection of `.md` files in `elegant-scipy/_build`

```bash
ls -l elegant-scipy/_build
```

