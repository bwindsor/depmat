# DepMat
GIT dependency management for Matlab. DepMat is for when your Matlab code depends on one or more GIT repositories, and you want these to be automatically kept up to date.
DepMat will check the current versions of the git repositories, and update to the newest version if required. DepMat will also add all subdirectories to the Matlab path automatically.

##Typical usage
Typically you might call `DepMatUpdate` whenever your Matlab program runs

```
DepMatUpdate(repoList);
````

where `repoList` is an array of `DepMatRepo` objects, each of which defines a repository on which your code depends.

An example function `TestRepoList` is provided, which generates an example list of repositories:

```
DepMatUpdate(TestRepoList);
````

You can substitute in your own function `MyRepoList`:

```
function repos = MyRepoList
    
    repos = DepMatRepo.empty;
    repos(end + 1) = DepMatRepo('apple', 'master', 'https://github.com/yourdomain/apple.git');
    repos(end + 1) = DepMatRepo('banana', 'master', 'https://github.com/yourdomain/banana.git');
    repos(end + 1) = DepMatRepo('orange', 'specialbranch', 'https://github.com/yourdomain/orange.git');
end
```

This will the check out and keep up to date the repository `apple` with URL `https://github.com/yourdomain/apple.git` on branch `master`, in a directory called 'apple' and so on.



## Suggested Setup (on each project)
* Create a file called `dependencies.m` with the following content. Modify the `DepMatRepo` calls to specify which other repositories your repo depends on. The format is `DepMatRepo(name, branch, bitbucketUrl)`
```
function dependencies()
    repos = DepMatRepo.empty;
    repos(end + 1) = DepMatRepo('leakbot-data-source', 'master', 'https://github.com/me/other-repo.git');
    repos(end + 1) = DepMatRepo('matlab-logging', 'master', 'https://github.com/me/logging.git');
    
    DepMatUpdate(repos, fileparts(mfilename('fullpath')));
end
```

## Updating dependencies to the latest version
* Now any time you want to update dependencies to the latest version, just run the function `dependencies()`

## Adding dependencies
Just add another item in the `dependencies` function

## Modifying/removing dependencies
This is tricky and **Depmat** doesn't do it for you. To modify a dependency, remove it and then add the thing you actually want. You need to do the following this to remove a dependency. In the examples `dependencies/matlab-logging` is used as the dependency to be removed - replace this with the one you actually want to remove.

* Delete the relevant section from the `.gitmodules` file.
* Stage the `.gitmodules` changes with `git add .gitmodules`
* Delete the relevant section from `.git/config`.
* Run `git rm --cached dependencies/matlab-logging`
* Run `rm -rf .git/modules/dependencies/matlab-logging`
* Commit with `git commit -m "Removed submodule"`
* Delete the now untracked submodule files with `rm -rf dependencies/matlab-logging`
