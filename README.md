# Latex-static-site

A template for converting your LaTeX document into a static website.

The process of turning the original LaTeX source code into a
static website is quite straightforward, making use of
[tex4ht](https://tug.org/tex4ht/)/[make4ht](https://github.com/michal-h21/make4ht). There were some tricky parts however, and some of the answers and examples on the web are outdated. To provide a template for others, I described the conversion and publishing process below.

# HOWTO

Steps to convert your LaTeX document:

## Install tex4ht

Install TexLive latex distribution > 2019. You want a recent version in order to have fully featured `tex4ht` and `make4ht` commands. If the default version in your distribution's repositories is outdated, install manually. For Ubuntu, you can use a script like [install-tl-ubuntu](https://github.com/scottkosty/install-tl-ubuntu)).

## Preprocess tex source files

- rename your main tex file to `index.tex`
    - required for links between pages to work (refs. to `index.html`)

- remove latex imports specific to PDF generation
    - this functionality won't be used and increases the probability of breaking the tex4ht build process

- remove commands that don't play well with MathJax:
    - `\mathrm`, `\textrm`, `rm`
    - to do this automatically, you could for example use the following regular expressions with Sublime text's 'replace in files' function: find: `\\mathrm\s*?\{(.+?)\}` -> replace: `$1`

- use simple bibtex for citations. Don't use biblatex or natbib.
    - Bibliography styles `unsrt`, `plain`, and `plainurl` worked well for me. More complex ones like `agsm` produced htlatex errors.

## Configure build process

I based my configuration files on https://github.com/michal-h21/tex4ht-enhanced-web with minor modifications. I altered and annotated the build files to make it clear what they are doing, and removed some superfluous options. The crucial files are:

- `src/config.cfg` - make4ht config file
- `src/build.mk4` - make4ht build file

Crucially, in order for compilation of the static html site structure to work, I needed to explicitly specify the output directory and build file in the `make4ht` command. My final build command was `make4ht -c config.cfg -e build.mk4 -d out index.tex "mathjax`.
This is the command executed by `build.sh`

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


