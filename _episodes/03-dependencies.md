---
layout: episode
title: "Recording dependencies"
teaching: 15
exercises: 15
questions:
  - How can we communicate different versions of software dependencies?
keypoints:
  - Capturing software dependencies is a must for reproducibility.
  - Files like `requirements.txt`, `environment.yml`, `Pipenv`, ..., should be part of the source repository.
  - Be skeptical when you see dependency lists without versions.
---

## Dependencies

Our codes often depend on other codes that in turn depend on other codes ...

- **Reproducibility**: We can control our code but how can we control dependencies?
- **10-year challenge**: Try to build/run your own code that you have created 10 (or less) years ago. Will your code
  from today work in 5 years if you don't change it?
- **Dependency hell**: Different codes on the same environment can have conflicting dependencies.

---

<a href="https://xkcd.com/1987/">
<img src="{{ site.baseurl }}/img/python_environment.png" style="height: 400px;" class="center">
</a>

## Conda, Anaconda, pip, Virtualenv, Pipenv, pyenv, Poetry, requirements.txt ...

These tools try to solve the following problems:
- Installing a **specific set of dependencies**, possibly with well defined versions
- **Recording the versions** for all dependencies
- **Isolate environments** on your computer for projects that have conflicting dependencies
- Isolate environments on computers with many users
- Using **different Python/R versions** per project
- Provide tools and services to **share packages**

Isolated environments are also useful because they help you make sure
that you know your dependencies!

---

> ## Exercise/discussion
>
> Compare these four `requirements.txt` solutions:
>
> **A**:
>
> Code depends on a number of packages but there is no `requirements.txt` file or equivalent.
>
>
> **B**:
> ```
> scipy
> numpy
> sympy
> click
> git+https://github.com/someuser/someproject.git@master
> git+https://github.com/anotheruser/anotherproject.git@master
> ```
>
> **C**:
> ```
> scipy==1.3.1
> numpy==1.16.4
> sympy==1.4
> click==7.0
> git+https://github.com/someuser/someproject.git@d7b2c7e
> git+https://github.com/anotheruser/anotherproject.git@sometag
> ```
>
> **D**:
> ```
> scipy==1.3.1
> numpy==1.16.4
> sympy==1.4
> click==7.0
> someproject==1.2.3
> anotherproject==2.3.4
> ```
{: .discussion}

---

## Pip and [PyPI](https://pypi.org)

- Python Package Index.
- Standard place to share Python packages.
- Also mixed-language packages are possible wrapped in a Python layer.
- Install a package:
```
$ pip install somepackage
```
- Install a specific version:
```
$ pip install somepackage==1.2.3
```
- Freeze the current environment into `requirements.txt`:
```
$ pip freeze > requirements.txt
```
- Install all dependencies listed in `requirements.txt`:
```
$ pip install -r requirements.txt
```
- Creating and sharing your own package: [https://packaging.python.org/tutorials/packaging-projects/](https://packaging.python.org/tutorials/packaging-projects/)
- It is possible to pip install from GitHub or other places:
```
$ pip install git+https://github.com/anotheruser/anotherproject.git@sometag
```

---

## [Conda](https://docs.conda.io/en/latest/)

<a href="https://medium.freecodecamp.org/why-you-need-python-environments-and-how-to-manage-them-with-conda-85f155f4353c">
<img src="{{ site.baseurl }}/img/conda_cartoon.jpeg" style="height: 300px;"/>
</a>

- Not only for Python: any language, also binaries.
- Created by Continuum Analytics, part of Anaconda/Miniconda, but can be installed standalone.
- Open source BSD license.
- Manages isolated software environments.
- Allows you to create and share conda packages.
- [Miniconda](https://docs.conda.io/en/latest/miniconda.html) is a lightweight alternative to [Anaconda](https://www.anaconda.com).
- Install a package:
```
$ conda install somepackage
```
- Install a specific version:
```
$ conda install somepackage=1.2.3
```
- Create a new environment:
```
$ conda create --name myenvironment
```
- Create a new environment from `requirements.txt`:
```
$ conda create --name myenvironment --file requirements.txt
```
- On e.g. HPC systems where you don't have write access to central
  installation directory:
```
$ conda create --prefix /some/path/to/env
```
- Activate a specific environment:
```
$ conda activate myenvironment
```
- Deactivate current environment:
```
$ conda deactivate
```
- List all environments:
```
$ conda info -e
```
- Freeze the current environment into `requirements.txt`:
```
$ conda list --export > requirements.txt
```
- Freeze the current environment into `environment.yml`:
```
$ conda env export > environment.yml
```
- To clean unnecessary cached files (which grow quickly over time):
```
$ conda clean
```

### Using conda to share a package

Conda packages can be built from a *recipe* and shared on
[anaconda.org](https://anaconda.org/) via
[your own private or public channel](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/create-custom-channels.html), or
via [conda-forge](https://conda-forge.org).

- conda-forge is a GitHub organization containing repositories of conda
  recipes.
- Has become the de facto standard channel for packages.
- Several continuous integration providers ensure that each repository
  ("feedstock") automatically builds its own recipe on Windows, Linux and OSX.

A step-by-step guide on how to contribute packages can be found in the
[conda-forge documentation](http://conda-forge.org/docs/maintainer/adding_pkgs.html).

To get an idea of what's needed, let's have a look at the
[boost feedstock](https://github.com/conda-forge/boost-feedstock/tree/2ceef9da69969ab3c0ae42817574b6c5b3219c99) (a set of C++ libraries). We see that:
- Every commit is tested on every platform.
- There's a list of maintainers.
- There's a [meta.yaml file](https://github.com/conda-forge/boost-feedstock/blob/2ceef9da69969ab3c0ae42817574b6c5b3219c99/recipe/meta.yaml) under the `recipe/` directory, along with (optional) `build.sh` and `bld.bat` files for building
  non-python code on OSX/Linux and Windows platforms.

---

> ## Exercise: Creating and exporting conda environments
>
> On Windows, we recommend to do this exercise in the Anaconda Prompt.
>
> Begin by **first importing** (by clicking "Use this template")
> and then cloning the 
> [word-count project](https://github.com/coderefinery/word-count).
> Then recreate the software environment provided by the
> `requirements.txt` file in the repository. 
> The `snakemake-minimal` package is only available in the `bioconda`
> channel, which needs to be specified:
> ```shell
> $ conda create --name wordcount --file requirements.txt --channel bioconda --channel conda-forge
> ```
> This will download all the packages listed in the `requirements.txt`
> file (with matching versions) along with all dependencies.
> The new environment also needs to be activated:
> ```shell
> $ conda activate wordcount
> ```
> We now have (roughly) the same environment as specified by the
> developers of the word-count project. But let's say we want to
> extend this environment, and share it with colleagues:
> - Inspect your available environments with `conda info -e`.
> - Install the `pandas` package using `conda install`.
> - Add `pandas` with the version you installed to the `requirements.txt` file.
> - Export the full environment using `conda env export > full_env.yml`, and 
>   compare the `.yml` file format to the `.txt` file format.
> - Create a new environment.yml file with the packages from the 
>   requirements.txt file. You only need the `channels` and `dependencies`
>   sections, and dependencies can be listed as `package=1.2.3` or simply 
>   `package`.
> - If you want to make sure your new environment.yml is correct, 
>   you can use it to create a new 
>   test environment using `conda env create -n <name> -f <file.yml>`. 
>   You can then delete it with 
>   `conda env remove <envname>` or simply remove the directory of the 
>   environment (that you can find using `conda info -e`).
{: .challenge}

---

## Other dependency management tools

### Python

- [Virtualenv](https://docs.python-guide.org/dev/virtualenvs/)
  - Example use:
  ```shell
  # creating a new env
  $ virtualenv myenvironment
  $ virtualenv --python=python3 myenvironment
  $ virtualenv /path/to/myenvironment
  # activating env, installing package and deactivating
  $ source myenvironment/bin/activate
  $ pip install somepackage
  $ deactivate
  ```
- [Pipenv](https://pipenv.readthedocs.io)
  - Alternative to virtualenv: you can activate and install in one step
  - Tool to easily manage per-project/per-directory Python **packages**
  - Easier than virtualenv to separate dependencies for development and usage
- [Poetry](https://poetry.eustace.io)
  - Alternative to virtualenv and Pipenv
- [Pyenv](https://github.com/pyenv/pyenv)
  - Tool to easily manage per-project/per-directory Python **versions**


### R

Good overview of use cases, strategies and tools for reproducible
environment at
[Reproducible Environments](https://environments.rstudio.com).

There are many tools available:
- [packrat](https://rstudio.github.io/packrat/)
  - Package from RStudio to isolate environment. Uses a packrat.lock file for storing dependencies
- [Jetpack](https://github.com/ankane/jetpack)
  - Designed as a CLI on top of packrat. Uses the DESCRIPTION file to store your project dependencies
- [RSuite](https://rsuite.io/)
- [automagic](https://github.com/cole-brokamp/automagic)
- [deplearning](https://github.com/MilesMcBain/deplearning)
- [devtools](https://github.com/r-lib/devtools)
- Are you using something else? Please send a
  [pull request](https://github.com/coderefinery/reproducible-research)!

### C/C++

There are no standard methods or tools to handle dependencies in
C/C++, but useful tools include: 

- [CMake](https://cmake.org/) 
  - Open-source, cross-platform family of tools designed to build,
    test and package software (see also the [CodeRefinery lesson on
    CMake](https://coderefinery.github.io/cmake/))
- [Conan](https://conan.io/)
  - A C/C++ package manager for Developers
- [Conda](https://docs.conda.io/en/latest/)
  - Works with any language, in principle

### Julia

The inbuilt package manager in Julia,
[Pkg.jl](https://pkgdocs.julialang.org/v1/), is designed around using
isolated environments with independent sets of packages. Environments
can either be local to a particular project or shared and selected by
name.

