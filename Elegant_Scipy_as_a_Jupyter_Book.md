# Can we turn Elegant Scipy into a Jupyter-book?

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/mwouts/jupyter-book-experiments/master?filepath=Elegant_Scipy_as_a_Jupyter_Book.md)

This document is a Bash Jupyter notebook that you can run interactively in Jupyter - simply click on the Binder link above.

And the code below owes much to Chris' [jupyter-book-deploy-demo](https://github.com/choldgraf/jupyter-book-deploy-demo/blob/master/netlify.toml).

## Create an environment with Jupytext and latest Jupyter Book

Here I will assume that you are working in a conda or a virtual environment. You could have used, for instance

```
conda create -n jpbook -y
conda activate jpbook
```

or 

```
virtualenv jpbook
source jpbook/bin/activate
```

In this environment we install the latest version of Jupytext

```bash
pip install jupytext>=1.2.4
```

And of Jupyter Book

```bash
pip install -U git+https://github.com/jupyter/jupyter-book
```

<!-- #region -->
You are also expected to have `ruby` available, and Bundler. Install Bundler with e.g.
```bash
gem install bundler -v '2.0.2'
```
<!-- #endregion -->

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

Jupyter Book seems to expect `.md` files rather than `.markdown` files. We rename the files:

```bash
# Associate the markdown files with a kernel
cd elegant-scipy/markdown/
jupytext --set-kernel elegant-scipy-kernel *.markdown --to md
rm *.markdown
cd ../..
```

Then we create the book itself

```bash
jupyter-book create elegant-scipy-as-a-jupyter-book --content-folder elegant-scipy/markdown
```

### Update the Table of Contents

Jupyter Book created a sample ToC for us - we'll review and edit it:

```bash
cat elegant-scipy-as-a-jupyter-book/_data/toc.yml
```

```bash
echo '- title: Elegant Scipy
  url: acknowledgements  
- title: Preface
  url: preface
- title: "Elegant NumPy: The Foundation of Scientific Python"
  url: ch1
- title: Quantile Normalization with NumPy and SciPy
  url: ch2
- title: Networks of Image Regions with ndimage
  url: ch3
- title: Frequency and the fast Fourier transform
  url: ch4
- title: Contingency tables using sparse coordinate matrices
  url: ch5
- title: Linear algebra in SciPy
  url: ch6
- title: Function optimization in SciPy
  url: ch7
- title: Big Data in Little Laptop with Toolz
  url: ch8
- title: Epilogue
  url: epilogue
' > elegant-scipy-as-a-jupyter-book/_data/toc.yml
```

### Update the `_config.yml` file

TODO: edit Author Name and description in this file:

```bash
cat elegant-scipy-as-a-jupyter-book/_config.yml
```

### Make sure the notebooks can run

The notebooks expect the `data` and `style` folders at the same level. Here we add a few symbolic links, but maybe (as Jupyter Book copies those folders) it would be better to change the notebooks to leave the folders where they are:

```bash
cd elegant-scipy-as-a-jupyter-book/content
ln -s ../../elegant-scipy/data/ data
ln -s ../../elegant-scipy/style/ style
cd ../..
```

### Execute the book

```bash
jupyter-book build elegant-scipy-as-a-jupyter-book/ --overwrite
```

The intermediate result is a collection of `.html` files in `elegant-scipy-as-a-jupyter-book/_build`

```bash
ls -l elegant-scipy-as-a-jupyter-book/_build
```

### Render the book locally


In theory we could build the book locally with `jekyll`. Unfortunately the `make install` part failed on my Ubuntu (sorry this is my first time with `ruby`)

```bash
cd elegant-scipy-as-a-jupyter-book
make install
bundle exec jekyll build
```

## Publish the book

```
cd elegant-scipy-as-a-jupyter-book
git init
```

Then we remove links to the original data and style from the `_build` and `content` folder:

```
cd _build
rm -rf data style sparse_table.txt
cd ../content
rm -rf data style sparse_table.txt
cd ..
```

Finally we publish the resulting site on github:

```
git add .
git commit -m 'Elegant Scipy as a Jupyter Book'
git remote add origin git@github.com:mwouts/elegant-scipy-as-a-jp-book.git
git push -uf origin master
```

Now our repo is online cf. https://github.com/mwouts/elegant-scipy-as-a-jp-book, and is indeed rendered as a Jupyter Book here: https://mwouts.github.io/elegant-scipy-as-a-jp-book.
