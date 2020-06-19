# Latex-static-site

A template for converting your LaTeX document into a static website.

The process of turning the original LaTeX source code into a
static website is quite straightforward, making use of [make4ht](https://github.com/michal-h21/make4ht) for [tex4ht](https://tug.org/tex4ht/)/. There were some tricky parts however, and some of the answers and examples on the web are outdated. To provide a template for others, I described the conversion and publishing process below.

# Usage

Simple usage

```bash
# clone the repository
git clone https://github.com/lkoelman/latex-static-site.git my-site
cd my-site

# creat the gh-pages branch for publishing
git checkout -b gh-pages

# import your latex documents and rename it
mv path/to/document.tex src/index.tex

# preprocess your tex files (see below)
your-preprocess-script.sh

# build the static site html
src/build.sh

# publish to github-pages (after creating repository)
git remote add published https://github.com/username/my-site.git 
git add -A
git commit -m "Add source and generated html"
git push -u published gh-pages

# navigate to github repository > Settings > GitHub pages > select branch 'gh-pages'
```

# Usage (detailed)

Steps to convert your LaTeX document:

## Install tex4ht

Install TexLive latex distribution > 2019. You want a recent version in order to have fully featured `tex4ht` and `make4ht` commands. If the default version in your distribution's repositories is outdated, install manually. For Ubuntu, you can use a script like [install-tl-ubuntu](https://github.com/scottkosty/install-tl-ubuntu)).

## Preprocess tex source files

Rename your main tex file to `index.tex`. This is required for links between pages to work (refs. to `index.html`).

Remove latex imports specific to PDF generation. They won't be used and only increases the likelihood of breaking the tex4ht build process.

Remove commands that don't play well with MathJax. Known commands are `\mathrm`, `\textrm`, `rm`. To do this automatically, you could use regular expressions or a preprocess script. For example, in Sublime Text editor you could use the following regular expressions in the 'replace in files' command: find: `\\mathrm\s*?\{(.+?)\}` -> replace: `$1`.

Use simple bibtex for citations. Don't use biblatex or natbib.
Bibliography styles `unsrt`, `plain`, and `plainurl` worked well for me. More complex ones like `agsm` produced htlatex errors.

## Configure build process

I based my configuration files on https://github.com/michal-h21/tex4ht-enhanced-web with minor modifications. I altered and annotated the build files to make it clear what they are doing, and removed some superfluous options. The crucial files are:

- `src/config.cfg` - make4ht config file
- `src/build.mk4` - make4ht build file

Crucially, in order for compilation of the static html site structure to work, I needed to explicitly specify the output directory and build file in the `make4ht` command. My final build command was `make4ht -c config.cfg -e build.mk4 -d out index.tex "mathjax`, which is the command executed by `build.sh`.

## Deploy to github pages

Clone this repository, do `git checkout -b gh-pages`, and put your latex code in `src/`. Then do:

```bash
src/build.sh
git add -A
git commit -m "Add html"
git push -u origin gh-pages
```

If you're publishing as a project site, you must use the `gh-pages` branch. This is not clear from the GitHub documentation at the time of writing. Otherwise, if it is a user/organization site (`user.github.io`), you can use the master branch.

Go to the settings page at https://github.com/username/repository/settings
Under the 'GitHub Pages' section, make sure that your site is being built
from the `gh-pages` branch.


