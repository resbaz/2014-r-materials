---
layout: lesson
root: ../..
Title: How to organize your project directories
tutor: Diego
---

**Materials**: If you have not already done so, please [download the lesson materials for this bootcamp](https://github.com/dbarneche/2014-10-31-USyd/raw/gh-pages/data/lessons.zip), unzip, then go to the directory `projects`, and open (double click) on the file `projects.Rproj` to open Rstudio.

The scientific process is naturally incremental, and many projects
start life as random notes, some code, then a manuscript, and
eventually everything is a bit mixed together.

<!-- more -->

<blockquote class="twitter-tweet"><p>Managing your projects in a reproducible fashion doesn't just make your science reproducible, it makes your life easier.</p>&mdash; Vince Buffalo (@vsbuffalo) <a href="https://twitter.com/vsbuffalo/status/323638476153167872">April 15, 2013</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

# Directory layout

## The general disaster

Most people tend to organize their projects like this:

![](bad_layout.png)

There are many reasons why we should *ALWAYS* avoid this:

1. It is really hard to tell what version of your data is
the original and what is the modified;
2. It gets really messy because it mixes files with various
extensions together;
3. It probably takes you a lot of time to actually find
things, and relate the correct figures to the exact code
that has been used to generate it;
4. You may face professional embarrassment when sharing your
project directory with a colleague or your supervisor.

## A niceR solution

A good project layout helps ensure the

* Integrity of data
* Portability of the project
* Easier to pick the project back up after a break

There is no one way to lay a project out. We have
different approaches for different projects, reflecting the history of
the project, who else is collaborating on that project.

Here are a couple of different ideas for laying a project out.  This
is the basic structure that I tend to use:

```
proj/
|-- R/
|-- data/
|-- output/
|-- |-- data/
|-- |-- figures/
|-- doc/
```

* The `R` directory contains various files with function definitions
  (but *only* function definitions - no code that actually runs).

* The `data` directory contains data used in the analysis.  This is
  treated as *read only*; in particular the R files are never allowed
  to write to the files in here.  Depending on the project, these
  might be csv files, a database, and the directory itself may have
  subdirectories.

* The `output/data` directory contains simulation output, processed
  datasets, logs, or other processed things. The `output/figures`
  directory contains the output figures generated by your code.
  Altogether the `output` directory *only contains generated files*;
  that is, I should always be able to delete the contents and regenerate them.

* The `doc` directory contains the paper. I work in Markdown which is
  nice because it can pick up figures directly made by R. Markdown
  is starting to get traction among biologists. With Word you'll have
  to paste them in yourself as the figures update.

In this set up, I usually have the R script files that *do* things in
the project root:

```
proj/
|-- R/
|-- data/
|-- output/
|-- |-- data/
|-- |-- figures/
|-- doc/
|-- analysis.R
```

For very simple projects, you might drop the R directory, perhaps
replacing it with a single file `analysis-functions.R` which you
`source` within the .R files that depend on the outputs.

The top of the analysis file usually looks something like

```r
library(some_package)
library(some_other_package)
source("R/functions.R")
source("R/utilities.R")
```

...followed by the code that loads the data, cleans it up, runs the
analysis and generates the figures.

Other people have other ideas

* [Carl Boettiger](http://www.carlboettiger.info/2012/05/06/research-workflow.html)
  is an open science advocate who has described his
  [layout in detail](http://www.carlboettiger.info/2012/05/06/research-workflow.html).
  This layout uses R packages for most of the code organisation, and
  would be a nice approach for large projects.

* [This article](http://www.ploscompbiol.org/article/info%3Adoi%2F10.1371%2Fjournal.pcbi.1000424)
  in [PLOS Computational Biology](http://www.ploscompbiol.org/)
  describes a general framework.

## Treat data as read only

This is probably the most important goal of setting up a
project. Data are typically time consuming and/or expensive to
collect. Working with them interactively (e.g., in Excel) where they
can be modified means you are never sure of where the data came from,
or how they have been modified. We suggest to put your data
into the `data` directory and treat it as *read only*. Within your
scripts you might generate derived data sets either temporarily (in an
R session only) or semi-permanently (as an file in `output/data`), but
the original data is always left in an untouched state.

## Treat generated output as disposable

In this approach, files in directory `output/` are all generated
by the scripts.  A nice thing about this approach is that if
the file names of generated files change (e.g, changing from
`phylogeny.pdf` to `mammal-phylogeny.pdf`) files with the old names
may still stick around, but because they're in this directory you know
you can always delete them. Before submitting a paper, I will go
through and delete all the generated files and rerun the analysis to
make sure that I can create all the analyses and figures from the
data.

## Separate function definition and application

When your project is new and shiny, the script file usually contains
many lines of directly executed code. As it matures, reusable
chunks get pulled into their own functions. The actual analysis
scripts then become relatively short, and use the functions defined in
scripts in `R`. Those scripts do nothing but define functions so that
they can always be `source()`'d by the analysis scripts.

# Setting up a project in RStudio

This gets rid of the #1 problem with most people's projects face;
where do you find the data. Two solutions people generally come up
with are:

1. Hard code the full file name for each file you load (e.g.,
`/Users/barneche/phd/ctotpaper/data/some_data.csv`)
2. Set the working directory at the beginning of your script file
`/Users/barneche/phd/ctotpaper/` then doing `read.csv("data/some_data.csv")`

The second of these is probably preferable to the first, because the
"special case" part is restricted to just one line in your file.
However, the project is still now quite fragile, because moving it
from one place to another, you must change this file. Some examples
of when you might do this:

* Archiving a project (moving it from a "current projects" directory
  to a new projects directory)
* Giving the code to somebody else (your lab mate, collaborator, supervisor)
* Uploading the code with your manuscript submission for review, or to
  [Dryad](http://datadryad.org/) after acceptance.
* New computer and new directory layout (especially changing
  platforms, or if your previous mess got too bad and you wanted to
  clean up).
* Any number of new reasons

The second case hints at a solution too; if we can start R in a
particular directory then we can just use paths *relative to the
project root* and have everything work nicely.

To create a project in R studio:

  - "Project": "Create Project..."
  - choose "New Project, (start a project in a new directory)".
  - Leave the "Type" as the default.
  - In the "Directory name" type the name for the project.  This might
    be `chapter_n` for a thesis, or something more descriptive like
    `fish_behaviour`.
  - In the "Create project as a subdirectory of" field select (type or
    browse) for the parent directory of the project. By default this
    is probably your home directory, but you might prefer your
    Documents folder.

**Acknowledgements**: This material was [adapted from the nice R code blog](http://nicercode.github.io/blog/2013-04-05-projects/) and modified by Diego Barneche
