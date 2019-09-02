# Can we turn Elegant Scipy into a Jupyter-book?

Give a try to the below on [![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/mwouts/jupyter-book-experiments/master?filepath=Elegant_Scipy_as_a_Jupyter_Book.md)!

## Create an environment with latest Jupyter Book and Jupytext

We create a new conda environment for this

```bash
conda create -n jupytextbook
conda activate jupytextbook
```

There we install a few standard packages (dependencies of Jupyter Book and Jupytext)

```bash
conda install jupyter numpy matplotlib tqdm pytest -y
```

Finally we install the latest version of Jupyter Book

```bash
git clone https://github.com/jupyter/jupyter-book.git
pip install jupyter-book
```

And of Jupytext

```bash
git clone https://github.com/mwouts/jupytext.git
pip install jupytext
```

## Download Elegant-Scipy

```bash
git clone https://github.com/elegant-scipy/elegant-scipy.git
```

# Install the book requirements

We could create a separate env for that, but for simplicity we use the same environment as above

```bash
pip install -r elegant-scipy/requirements.txt
python -m ipykernel install --name elegant-scipy-kernel --user
```

We also inject the current kernel into the Markdown file (later on, if possible, I'd prefer to pass the desired kernel as an option to Jupyter book)

Unfortunately the below does not work - to be fixed at https://github.com/mwouts/jupytext/issues/325

```bash
# jupytext --set-kernel elegant-scipy-kernel elegant-scipy/markdown/*
```

So I'll do it manually:

```bash
for MDFILE in elegant-scipy/markdown/*.markdown; do
echo '---
jupyter:
  jupytext:
    text_representation:
      extension: .markdown
      format_name: markdown
      format_version: '1.1'
      jupytext_version: 1.2.3
  kernelspec:
    display_name: elegant-scipy-kernel
    language: python
    name: elegant-scipy-kernel
---

' | cat - $MDFILE > /tmp/concat
mv /tmp/concat $MDFILE
# rename .markdown to .md extension?
# mv /tmp/concat "${MDFILE%.markdown}.md"
done
```

## Create our Jupyter Book

We first create our book

```bash
jupyter-book create elegant-scipy-book --content-folder elegant-scipy/markdown --license elegant-scipy/LICENSE.md
```

Then we update the table of contents (with only two chapters for now)

```bash
echo '- title: Elegant Scipy
  url: /elegant-scipy/acknowledgements
  not_numbered: true
  expand_sections: true
  sections:
  - title: Preface
    url: /elegant-scipy/preface
    not_numbered: true
  - title: "Elegant NumPy: The Foundation of Scientific Python"
    url: /elegant-scipy/ch1
  - title: Quantile Normalization with NumPy and SciPy
    url: /elegant-scipy/ch2
  - title: Epilogue
    url: /elegant-scipy/epilogue
    not_numbered: true
' > ./elegant-scipy-book/_data/toc.yml
```

and finally, we build the book

```bash
jupyter-book build elegant-scipy-book
```
