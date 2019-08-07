# Git submodules
commands to execute to initialize a git repository with submodules

Reference: https://git-scm.com/book/en/v2/Git-Tools-Submodules



## Getting started

Let's assume we have a repository (`dummy-submodule` in this case) that we want to incorporate into a new (or existing repository). Let's just work within this one.

If you haven't already, clone this repo.

```
git clone git@github.com:njchiang/submodule-practice.git
```

## Adding a submodule
Let's add the `dummy-submodule` repository using `git submodule`. in this case we'll rename it `my-submodule`

```
git submodule add https://github.com/njchiang/dummy-submodule my-submodule
```

And that's it! If you don't plan on updating the submodule, git will recognize that this is a separate repo. You can work as normal.

## Pulling changes to the submodule
to update the submodule, there are 2 options
1. `cd` into the directory and commit as normal:
```
cd my-submodule
git fetch
git pull # OR git merge origin/master
cd ..
```
2. use `submodule update`
```
git submodule update --remote my-submodule
```

By default, submodule will assume you want to pull the master branch. if we want this to change, we can do something like: 

```
git config -f .gitmodules submodule.utils.branch branch-name
```

the `-f .gitmodules` option makes it so that anyone else cloning the repository will also point to that branch (instead of master). This is probably preferable. If you don't specify it, then only you will be using the branch and everyone else will be using master.

A quirk of this is that using `git submodule update` leaves the submodule in a detached HEAD state, that is, without a working branch. If you'll only be pulling updates from the submodule repo, this is fine, but if you want to write changes as well, you need to set the submodule up with a working branch. 

If you need to make a change to the submodule, though, 

```
# from the submodule directory
git checkout branch-name
```

Your update calls also need to be updated to specify how the update should happen:

```
git submodule update --remote --merge my-submodule
# or
git submodule update --remote --rebase my-submodule
```


## Pushing changes to the submodule
Remember that updating the submodule could potentially affect other users' work. However, if you want to conveniently work on both repos at the same time, you can just `cd` into the submodule directory and commit and push as you would a normal repository. However, merge conflicts are incredibly difficult to resolve here, so _don't let them happen_.

Also, by default, `git push` will not push the submodule changes. Specify `--recurse-submodules=check` or `--recurse-submodules=on-demand` to make sure all submodule changes have already been pushed.


## Removing a submodule
There are two steps to removing a submodule.
1. Delete the relevant line from the .gitmodules file

```
git submodule deinit -f -- submodule
```

2. delete the directory

```
git rm submodule
```

## Cloning a repo with submodules

If you simply call `git clone` on a repository that has submodules, the submodule contents will not be downloaded (though the folders will exist). There are two ways to get download the submodules:

### Option 1: explicitly initialize submodule
```
git clone [REPOSITORY WITH SUBMODULES]
```

notice that the "utils" directory is empty
```
git submodule init
git submodule update
```
### Option 2: clone the submodules
```
git clone --recurse-submodules [REPOSITORY WITH SUBMODULES]
```
