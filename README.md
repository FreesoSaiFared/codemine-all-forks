# codemine-all-forks


GOAL Analyze forks network to find interesting forks, commits, file changes.
Use LLMs to analyse the database. Integrate this functionality with VScode cline and other sourcecode analysis frameworks. 


## Why build it?

* At this moment it is impossible to get a reliable and complete description of what a fork adds and what code it contains. This means that it is likely that sometimes features get implemented twice, or that approaches are missed completely. The idea of downloading all forks of a project should become a standard feature of git, perhaps.  

* Frustration with navigation forks graph / network on GitHub
* Too many forks with nothing interesting and noisy commits
* Wanting to find changes made to a file, function or a set of lines across the network to avoid duplicating the work (fixing similar bugs or building similar features)

## How does it work?

* Gets all forks and all branches into a single repository
* Make available git data mining tools such as `gitinspector` and `git_stats`. Use the tools in this repository as they have been modified to work across all branches. *ADD LLM Descriptions of Code*
* View other `Tips` below on how to search across the forks network using Git commands





## How to install it?

*This i would ideally want to integrate with a version of cline, this is a visual code extension that uses LLM and context to execute commands locally. A modified version of cline should instead of code generation, be customized to automate many of the analysis tasks that are described below. This will require creataing prompts that instruct this version of 'cline-forkmine' to integrate this repo and to have access to all tools mentioned below. 



```bash

DONE THIS ON UBUNTU SERVER VIA SSH

git clone https://github.com/hbt/git-forks-analysis
cd git-forks-analysis
docker-compose pull 

```

## How to use it?
EVERYTHING BELOW SHOULD BE CONVERTED TO A PROMPT FORMAT SO THAT A USER CAN INTERACTIVELY QUERY THE FORKS IN A CHAT 

To generate HTML visualization of forks

```bash

cd bin

./analyze-forks username repository

# retrieve all direct forks for https://github.com/hbt/mouseless
./analyze-forks hbt mouseless

# retrieve all forks recursively (the whole network) https://github.com/frost-nzcr4/find_forks -- purposefully chose a small repo to avoid running this by mistake
./analyze-forks-deep frost-nzcr4 find_forks

```

Calling the `gitinspector` directly via CLI

```bash

./bin/gitinspector /out/mouseless
./bin/gitinspector --grading=true /out/mouseless
./bin/gitinspector --grading=true  --format=html /out/mouseless

```

## What does it look like?

* gitinspector of mouseless [mouseless HTML](http://htmlpreview.github.io/?https://github.com/hbt/git-forks-analysis/blob/master/example/mouseless/mouseless.html)
* gitinspector CLI output [mouseless CLI](/example/mouseless/mouseless.txt)
* quick_stats of mouseless [mouseless quick stats](http://htmlpreview.github.io/?https://github.com/hbt/git-forks-analysis/blob/master/example/mouseless/git_stats/general.html)

## How to find interesting forks?

* Forks with stars / watchers -- use https://techgaun.github.io/active-forks/index.html
* Looks for commits count, insertions and deletion counts


## How to search for commits per file across all forks and branches?

```bash

cd out/mouseless
git log --all background_scripts/extension-reloader.js

# include diffs
git log -p --all background_scripts/extension-reloader.js


```

## How to search for changes in a function across all forks and branches?

```bash

# look for contributions to a specific function across all forks
git log --all -p -L ":getCenters":lib/model/PersonInfo.php --ignore-all-space --ignore-space-change --ignore-space-at-eol --ignore-blank-lines


# also accepts line numbers range
git log --all -p -L 13,20:lib/model/PersonInfo.php --ignore-all-space --ignore-space-change --ignore-space-at-eol --ignore-blank-lines

#modify ~/.gitattributes to add language support
#*.php diff=php
#*.js diff=node

#normalize the repo in case of ^M
#Note: this might corrupt some file formats (e.g binary, images etc.)
#https://superuser.com/questions/293941/rewrite-git-history-to-replace-all-crlf-to-lf

#**Note: perform all operations on tmpfs. Much faster**
#normalize the whole repo and its history
# specific file  (much faster) -- few minutes
git filter-branch --tree-filter 'git ls-files lib/model/PersonInfo.php -z | xargs -0 fromdos' -- --all

#whole repo but takes longer
git filter-branch --tree-filter 'git ls-files -z | xargs -0 fromdos' -- --all


#Alternative if language is not properly supported is to use pickaxe
git log --all -p -S"function createHints"  content_scripts/hints.js

# for some languages, --function-context works well
git log --all -p -ScreateHints --function-context content_scripts/hints.js


```

## How to search commits that have not been merged?

```bash

git log -p --all --not master --no-merges

```

## How to find most modified files in forks?

```bash

git log --all --pretty=format: --name-only --not master --no-merges | sort | uniq -c | sort -rg | head -10

```


## Other git data mining tools worth a mention

* [https://github.com/askgitdev/askgit](https://github.com/askgitdev/askgit) -- Query git repositories with SQL.
* [https://github.com/src-d/gitbase](https://github.com/src-d/gitbase) -- Turns Git Repository into an SQL database
* [https://github.com/src-d/hercules](https://github.com/src-d/hercules)
* [https://github.com/arzzen/git-quick-stats](https://github.com/arzzen/git-quick-stats) -- interactive tool
* [https://github.com/src-d/vecino](https://github.com/src-d/vecino) -- Recommends similar repositories 
* [https://github.com/src-d/msc](https://github.com/src-d/tmsc) -- Recommends topics per repo based on content
* [https://github.com/jgehring/pepper](https://github.com/jgehring/pepper) -- not tried
* [https://github.com/brendangregg/FlameGraph](https://github.com/brendangregg/FlameGraph) -- not tried but lots of stars
* [https://github.com/tj/git-extras](https://github.com/tj/git-extras) -- git-effort amongst others
* [https://github.com/ejwa/gitinspector](https://github.com/ejwa/gitinspector) -- used in this repo to create CLI git stats
* [https://github.com/tomgi/git_stats](https://github.com/tomgi/git_stats) --used in this repo to create HTML git stats
* [https://pydriller.readthedocs.io/en/latest/intro.html](https://pydriller.readthedocs.io/en/latest/intro.html) -- python api to parse git commits
* [https://github.com/arzzen/git-quick-stats/](https://github.com/arzzen/git-quick-stats/) -- CLI tool to browse contributors stats


## Contribute: Get in touch if you have a git data mining tool recommendation
