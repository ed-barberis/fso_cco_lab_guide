# Partner Lab Template

Project template for creating partner labs.

## Prerequisites

- Install [Hugo](https://gohugo.io/) on your workstation
- Your favorite text editor.
- A working knowledge of Markdown (see [here](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) for a reference).

## How to Create Content

- Create a fork of this repo to your own repo that will host the lab content.
- Clone your forked repo to your workstation.
- Add content in Markdown language to the `content` folder.
  - Folders can be added. They will be displayed in the side menu in alphabetical order. There are sample files to show how content is organized.

### Download the theme locally

The lab template repo has a dependency on the `hugo-theme-learn` git project from `matcornic`. In order to test content locally and eventually build the project, you will need to download the theme (it is set up as a submodule within this project).

Follow these steps if the theme does not automatically download when you clone the repo. From the directory of your repo fork, run `git submodule update --init --recursive` to download the contents of the theme.
