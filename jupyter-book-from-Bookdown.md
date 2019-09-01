# Can we turn a R-book repository into a Jupyter-book repository?

```bash
cd geocompr
```

## Create the conda env

```bash
conda create -n geocompr
conda activate geocompr
```

## Install Jupyter Book

```bash
# Jupyter book dependencies
conda install jupyter numpy tqdm pytest -y
conda install jupytext -c conda-forge -y
git clone https://github.com/jupyter/jupyter-book.git
cd jupyter-book
pip install -e .
cd ..
```

## Download Geocomputation with R

```bash
git clone https://github.com/Robinlovelace/geocompr.git
```

## Create our Jupyter Book

```bash
## This did not work as `jupyter-book toc` does not have an `--overwrite` argument
# jupyter-book create geocompr-jpbook --content-folder geocompr
```

```bash
# Create book
jupyter-book create geocompr-jpbook
# Copy chapters
cp geocompr/??-*.Rmd geocompr-jpbook/content

# TODO from here

# Rebuild TOC
jupyter-book toc geocompr-jpbook

# TODO: setup the R kernel with appropriate env, see https://github.com/Robinlovelace/geocompr/blob/master/.binder/Dockerfile#L10

# TODO: call jupytext.kernels.set_kernelspec_from_language in jupyter-book 
# when we want to execute a notebook that has no kernel info

# Build book
```

