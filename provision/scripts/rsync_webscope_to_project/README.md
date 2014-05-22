# Webscope to project rsync script


## IMPORTANT - PLEASE READ

Webscope is still under heavy development and as such, the effects of running this script may change between versions. Only run this script to update your projects if you are aware of recent changes in Vlad. If you're unsure, backup your destination project before running this script.

## Description

Use this script (````webscope-rsync.sh````) to copy Webscope's files from this instance of Webscope to a new or existing project directory.
Unnecessary files will not be copied (.git and so on). See the exclude & include lists beside this script for further details.

### Files may be deleted
Any old Webscope files found at the destination path that are no longer required will be deleted.

## Usage

Ensure this script is executable and then run it passing the destination path as an argument.

For example:

```
$ path_to_webscope/webscope/scripts/rsync_webscope_to_project/webscope-rsync.sh path/to/your/project

```
