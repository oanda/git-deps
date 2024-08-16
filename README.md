## git-deps - script to manage source dependencies with git

Copyright (C) 2023 OANDA Corporation

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU Affero General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU Affero General Public License for more details.

You should have received a copy of the GNU Affero General Public License
along with this program.  If not, see <https://www.gnu.org/licenses/>.

### What is this

TL;DR: this is a lightweight alternative to git submodules that doesn't track
specific commits of the repos, but instead tracks the latest commit on a branch
or tag.

This is a bash script that helps to manage a predefined set of git repositories
that should be checked out somewhere inside your project. You define the
repositories and their checkout locations in a file `.gitdeps` in your project
root, commit it, and then run this script to either clone the repos or update
existing clones with the latest commits.

### Install

Copy the file `git-deps` into any directory on your device that is in your
`PATH` list. Eg I have mine in `~/bin`. Then make sure the file has executable
mode: `chmod 755 git-deps`.

### Set up project

Commit a file `.gitdeps` in your project's root directory, with the following
structure:

    deps/abc;git@host.com:user/abc;main
    # this is a comment
    deps/def;git@host.com:user/def;v1.2.0

This is a semicolon-separated structured plain text file, with the fields on
each line interpreted as follows:

1. Location relative to the project root to check out the repo
2. URL of the remote for the repo
3. Branch or tag name to check out the repo

Optionally, you can have other fields after the first three; they will be
ignored by the script.

### Local development

In your project, make sure you have no uncommitted changes. Then, run `git
deps`.

This will firstly do a `git pull`. Then, it will either clone or update all the
defined repos to the latest available commit from the remote. If the remote URL
changes, it will also change the remote in the checked out repo and pull from
the new URL instead. It will update each repo in parallel. Finally, it will
recursively walk through the checked out repos and check out or clone any repos
as found in `.gitdeps` file in those.

When this is done, your project should be up-to-date with the latest changes.
Effectively, think of this as a replacement for `git pull --recurse-submodules`.

## In CI pipelines

If the script detects that the `CI` environment variable is set, it will do
_shallow clones_ of all the defined repos in `.gitdeps`, at the given branches
or tags.

