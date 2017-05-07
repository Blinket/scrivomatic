# Scrivomatic: Scrivener + Pandocomatic (or Panzer)#

## Introduction ##

[Scrivener](http://literatureandlatte.com) is a program for all sorts of writers, which handles the structural organisation and constructive process of writing with greap aplomb. It lets you write and organise your writing and reference materials without having to worry about the final "look". The final "look" is handled by a process called compiling, where you can choose the output format and select contents with great flexibility. Although Scrivener uses rich-text internally, it has very good integration with the plain text [Markdown](https://en.wikipedia.org/wiki/Markdown) format, for which there are a great number of powerful converters. Markdown offers a number of advantages over rich-text with Scrivener: it creates more structured documents without lots of fussing in a Word processor or layout software. For example: figures & figure captions, block quotes, code blocks (with code highlighting) and others are all semantically styled, headings get proper outline levels by default, amongst other advantages. *And for academics, you can even generate a full [Bibliography](http://pandoc.org/MANUAL.html#citations)*. This save you time, especially if you compile regularly during collaborative editing.

The most powerful of all Markdown converters is [Pandoc](http://pandoc.org/index.html), which allows you great flexibility and power in how markdown gets transformed to **many** outputs. And you can use customised templates for Word, LibreOffice, HTML, LaTeX and many other formats. So for example you create a `custom.docx` where you specify your preferred styles/fonts/paragraph/page layouts in Word, and Pandoc uses this when generateing your DOCX from Scrivener. The only real downside to Pandoc is a great many options that have to be specified on the command line. To make this much easier, tools like [Pandocomatic](https://heerdebeer.org/Software/markdown/pandocomatic/) and [Panzer](https://github.com/msprev/panzer), allow you to set up "styles", where for each output format you can set up all the multitudinous pandoc options in a configuration file. They also allow you to run pre– and post–processors for more advanced users. In Scrivener you simply specify the style you want for your document, and all the settings are activated and Pandoc is run without any other fussing. 

For example, in Scrivener, you create a front matter document with a metadata block right at the top like so:

```yaml
---
title: "<$projecttitle>"
author:
  - John Doe
  - Joanna Doe
pandocomatic_:
  use-template:
    - paper-with-refs-html
    - paper-with-refs-docx
---

```

And compile to a Multimarkdown file `mydoc.md`. Then you just run `pandocomatic mydoc.md` and it handles the pandoc compile using the specified template (in this case both a HTML and a DOCX file are generated from the same Scrivener compile document). The pandocomatic configuration file could look like this for DOCX, generating a bibliography with the APA style amongst other things:

```yaml
  paper-with-refs-docx:
    preprocessors: []
    pandoc:
      from: markdown
      to: docx
      standalone: true
      filter: pandoc-citeproc
      bibliography: core.bib
      citation-style: apa.csl
      reference-docx: templates/custom.docx
      dpi: 300
      toc: false
      base-header-level: 1
      metadata: 
        - "notes-after-punctuation=false"  
        - "link-citations=true"
    postprocessors: []
```

## Requirements ##
You need to install Pandoc and either Pandocomatic (uses Ruby) or Panzer (uses Python). This requires a minimal amount of typing into the terminal. I prefer Pandocomatic as it is a bit more flexible in output options, but Panzer is more powerful in specifying the input options. For macOS you can [install manually](http://pandoc.org/installing.html), but it is better to use [Homebrew](https://brew.sh/) to install Pandoc as `brew` can keep everything up to date:

```shell
> brew install pandoc
```

You use Ruby's `gem` to install Pandocomatic (putting the binary in `/usr/local/lib`):

```shell
> sudo gem install pandocomatic -u /usr/local/bin
```

And Python's `pip` if you want to use Panzer instead:

```shell
> pip install git+https://github.com/msprev/panzer
```

## Configuration ##
Please read the documentation for pandocomatic to understand the full plethora of options. A sample file is included above. You need to put this pandocomatic configuration file `pandocomatic.yaml` in the pandoc DATA-DIR, which is `$HOME/.pandoc` by default. If you want to use Panzer, it uses a file called `styles.yaml` placed in `$HOME/.panzer`. I place my custom templates in `$HOME/.pandoc/templates` and my bibliography and style files in `$HOME/.pandoc`

## scrivomatic wrapper script ##
I have also made a small tool, [`scrivomatic`](https://github.com/iandol/scrivomatic/raw/master/scrivomatic), which can be run from anywhere and ensures the search path and environment are correct for pandocomatic, panzer and pandoc. By default, Scrivener cannot run pandocomatic or panzer as it uses a restrictive environment. I use `scrivomatic` to enable Scrivener to trigger the tools properly. Save `scrivomatic` to ~/Downloads, then move it to a directory on your path. 

```shell
mkdir -p $HOME/bin
mv $HOME/Downloads/scrivomatic $HOME/bin
chmod 755 $HOME/bin/scrivomatic
```

To run scrivomatic from Scrivener (with a `-v` verbose log put on your desktop, make sure you replace YOURUSERNAME with your macOS user name):

```
path: /Users/YOURUSERNAME/bin/scrivomatic
Arguments: -i "<$inputfile>" -o "<$outputname>.docx" -v >> /Users/YOURUSERNAME/Desktop/scrivomatic.log 2>&1 
```

Then use your magic sauce to trigger this on a Scrivener compile automagically.
