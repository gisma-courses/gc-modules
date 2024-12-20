---
title: "Project-Oriented Workflow"
author: "Chris Reudenbach"
date: 2021-11-13
slug: []
toc: true
output:
  blogdown::html_page:
    keep_md: yes
categories: ["aGIS"]
tags: ["reproducibility", "scripts","project management"]
description: 'We value freedom of choice as an important good but giving our long-term experience freedom of choice comes to an end when we talk about the mandatory working environment for this course.'
image: '/img/code.png'
weight: 99
---

## Flexible but reproducible setup

To set up a working or project environment, (normally) the first steps are defining different folder paths and loading the necessary R packages as well as additional functions.

If you also need to access additional software, like GIS, the appropriate binaries and software environments must be linked, too. Factoring in the major operating systems and the potentially multiple versions of software installed on a single system results in almost unlimited combinations of set ups.

We value freedom of choice as an important good. But given our long-term experience as instructors of similar courses, there is **no freedom of choice** when it comes to the mandatory working environment for this course. The reason for this is simple: Assignments and chunks of code written on person A’s laptop should run on person B’s computer without requiring any changes. The greater the number of systems that should be able to run the code, the nastier this potential situation can become. So, let’s save everyone’s time and focus on the things that are really important. Once the course is finished, feel free to use any working environment structure you like.

## R project frameworks

Setting up a working or project environment requires us to define different folder paths and load necessary R packages and additional functions. If, in addition, external APIs (application programming interface) are to be integrated stably and without great effort, the associated paths and environment variables must also be defined correctly.

There is a growing number of R packages, such as e.g. [`workflowR`](https://jdblischak.github.io/workflowr/), [`usethis`](https://usethis.r-lib.org/), [`tinyProject`](https://github.com/FrancoisGuillem/tinyProject) or [`projects`](https://github.com/NikKrieger/projects) that provide a wide range of functions for such issues.

For this introduction to a structured organization of R-based development projects, we suggest the [`envimaR`](https://github.com/envima/envimaR) package which was written for the needs of this course. It is a lightweight project management package that takes care of the basic project related tasks with respect to the special features of the University Labs. It can be installed as follows.

    devtools::install_github("envima/envimaR")

Essentially, a project may be split at least in three categories of tasks:

-   data (storage, cleaning/preprocessing…)
-   scripts (for handling the data performing analysis, creating output…)
-   documentation (compiling text, figures, reports…)

The basis of the aforementioned categories is an adequate storage structure on a suitable permanent storage medium (hard disk, USB stick, cloud, etc.). We suggest a meaningful hierarchical directory structure.

## Coping with different absolute paths across different computers

The biggest problem when it comes to cross-computer project path environments is not the project environment itself, i.e. the subfolders of your project *relative* to your project folder but the *absolute* path to your project folder. Imagine you agreed on a project folder called mpg-envinsys-plygrnd. On the laptop of person A, the absolute path to the root folder might be `C:\Users\UserA\University\mpg-courses\gi` while on the external hard disk of person B it might be `X:\university_courses\mpg-courses\gi`. If you want to read from your data subfolder you have to change the absolute directory path depending on which computer the script is running. Not good.

One solution to this problem is to agree on a common structure *relative* to your individual R home directory using symbolic links (Linux flavor systems) or junctions (Windows flavor systems).

Example: Create a top-level folder name which hosts all of your student projects. For example, this folder is called `edu`. Within `edu`, the project folder of this course is called `course-name-provided-by-the-instructor`. Create the folder `edu` anywhere you want, e.g. at `D:\stuff\important\edu`. Then create a so called symbolic link to this folder within your R home directory i.e. the directory where the R function `path.expand("~")` points to.

To create this link on Windows flavor systems, start a command prompt window (e.g. press \[Windows\], type “CMD,” press \[Return\]) and change the directory of the command prompt window to your R home directory which is by default `C:\Users\your-user-name\Documents`. Then use the `mklink \J` command to create the symbolic link. In summary and given the paths above:

``` bash
cd C:\Users\your-user-name\Documents
mklink /J edu D:\stuff\important\edu
```

On Linux flavor systems, the R home directory is your home directory by default, i.e. `/home/your-user-name/`. If you create your edu folder on `/media/memory/interesting/edu`, the symbolic link can be created using your bash environment:

``` bash
cd /home/<your-user-name>/
ln -s /media/memory/interesting/edu edu
```

Now one can access the `edu` folder on both the Windows and the Linux example via the home directory, i.e. `~/edu/`. All problems solved.

While this will work on all of your private computers, it will **not** work on the ones in the University’s computer lab. To handle that as smooth as possible, you can use the functionality of the `envimaR` package which allows to set defaults for all computers but one special type which is handled differently.

## Basic workflow of the setup procedure

There are two aspects we would draw your attention to:
1. Define everything once not twice: It is a good idea to define your project
folder structure, the required libraries and other settings in one place. A simple
approach is a setup script, i.e. something like `course-name-provided-by-the-instructor_setup.R` that is sourced
into each control or analysis script of your project.
1. Use the same project folder structure in a team: Use the same folder structure
relative to a fixed starting point across all computers and devices of your team.
As a root folder, a folder in your home directory (e.g. as described before `~/edu`) is the recommended choice.
Within this folder you can directly store your projects or use a symlink to any
other place on your storage devices.

### Special considerations at Marburg University computer labs

The R home directory on the computers in the labs at Marburg University points to
the network drive `H:/Documents`. For storage space reasons, you could not use
this drive for symlinks neither for the storage of the R-packages that you need to install. Therefore, you have to define an alternative starting point
for your project folders to work with.

To handle your project relative on all computers using something beneath your
home directory `~` as a starting point except the
University computer labs, we therefore recommend to use the `envimaR` package.

### Conceptual Guideline

For the course it will be assumed that:

-   your local starting point is `~/edu`,
-   your project is called `course-name-provided-by-the-instructor`,
-   your git repository is called `course-name-provided-by-the-instructor`,
-   your setup script is called `course-name-provided-by-the-instructor_setup.R`
-   located at `course-name-provided-by-the-instructor/src`.

This setup concept is a bit more than a guideline, it is a rule. Read it, learn it, live it and have a nice ride.

## Wrap it up in a setup script

Provided that I want to create a project with the mandantory folder structure, checking the PC (Personal or University) that I am working on, load all packages that I need and store all pathes in a list for later use, I have to define these settings and call the `createEnvi()` function to set up the basic working environment.

In addition, we can use the setup function to define project related useful settings. E.g. it makes sense to to set an option for temporary actions in the `raster` package define some standard color palettes and so on.

If we put everything together in one script, it looks like this:

<script src="https://gist.github.com/gisma/3dfbdd4de0d5b23e51df9885475da82f.js"></script>

{{% alert "IMPORTANT" "danger" "black" "black" "During the course it is mandantory to save this script in the *<src>* folder named *<course-name-provided-by-the-instructor_setup.R>*. Best practise is to source this script each time when you run an analysis etc. that is connected with this project." %}}

{{% alert "NOTE" "info" "black" "black" "The first call of the above script needs some time for execution. If you encounter errors during this installation process, try to install the packages separately for making troubleshooting more convenient. In addition - please *check* the result by navigating to the directory using your favorite file manger. In addition please check the returned *<envrmt>* list. It contains all of the paths as character strings in a convenient list structure" %}}

## Template control script

It is strongly recommended that you use the following basic logic to start preprocessing analysis and all other project related tasks like analysis etc. You should use a common template for creating each new script that at least contains the following elements.

<script src="https://gist.github.com/gisma/99a429cb9f2ce800ec7783d063ad360b.js"></script>

Since the processing of data in a project usually requires a serial sequence of tasks, it makes sense to number these scripts. So something like this:
- 10-data-download.R
- 20-data-cleaning.R
- 30-data-analysis.R
- 40-figures.R

Thus to summarize the above approach the basic setup script:

-   creates/initializes the mandatory basic folder structure
-   creates a list variable containing all paths as shortcuts  
-   installs and initializes all packages and settings for the project

all of which follow the logic of the template control script, are responsible for data acquisition, cleansing, analysis and result generation.

## Exercises

-   Implement and check the recommende working environment setup

### Concluding remarks

{{% alert "REMEMBER" "info" "black" "black" "Several errors are likely to pop up during this installation and setup process. As you will learn through this entire process of patching together different pieces of software, some error warnings are more descriptive than others." %}}

This procedure is typical and usually necessary. For complex tasks, external software libraries and tools often need to be loaded and connected. Even if most software developers try to facilitate this to make it easier, it is often associated with an interactive and step-by-step approach.

When in doubt (**and before asking your instructor ;-)**), ask Google! Not because we are too lazy to answer (we will answer you, of course) but because part of becoming a (data) scientist is learning how to solve these problems. We all learned (and continue to learn) this way as well and it is highly unlikely that you are the first person to ask whatever question you may have – Especially [StackOverflow](https://stackoverflow.com/questions/tagged/r) is your friend!

## Readings

For a deeper understanding and some good reasons why we are making such an effort, the following links are helpful

-   [Project-oriented workflow](https://www.tidyverse.org/blog/2017/12/workflow-vs-script/) or *“I set your Computer on fire”* by Jenny Brian
-   Krieger NI, Perzynski AT, Dalton JE (2019) Facilitating reproducible project management and manuscript development in team science: The projects R package. PLOS ONE 14(7): e0212390. [https://doi.org/10.1371/journal.pone.0212390](https://journals.plos.org/plosone/article/file?id=10.1371/journal.pone.0212390&type=printable)

## Comments & Suggestions
