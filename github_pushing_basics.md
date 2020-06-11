# Basics of getting files and repos on github

Pre reqs:

- An installation of git (See [here](https://gist.github.com/derhuerst/1b15ff4652a867391f03) for more help)

Then navigate using `cd` to where you want to be on your computer and clone the repo you want. Example:
`git clone https://github.com/biovcnet/topic-amplicons.git`

Then modify/add files, directories, etc.

Return to terminal and:

`cd` into local copy of git repo

`git status` should report any changes that have happened

`git add *` will add those to what we want to “commit”

then running `git status` again should say something different, like 2 changes ready for commit or something

`git commit -m "adding material to repo"`  will commit them, and the -m flag lets you add little note about what you are doing

That’s a change to your local master branch, and now we’re going to make that the main master branch with `git push`