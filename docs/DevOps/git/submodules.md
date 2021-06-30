
# Adding a submodule

In order to add a Git submodule, use the “git submodule add” command and specify the URL of the Git remote repository to be included as a submodule.

Optionally, you can also specify the target directory (it will be included in a directory named as the remote repository name if not provided)

```shell
git submodule add <remote_url> <destination_folder>
```

When adding a Git submodule, your submodule will be staged. As a consequence, you will need to commit your submodule by using the “git commit” command.
```shell
git commit -m "Added the submodule to the project."
git push
```

# Removing a submodule

From [here](https://stackoverflow.com/questions/1260748/how-do-i-remove-a-submodule/16162000)

```text
0. mv a/submodule a/submodule_tmp

1. git submodule deinit -f -- a/submodule    
2. rm -rf .git/modules/a/submodule
3. git rm -f a/submodule
# Note: a/submodule (no trailing slash)

# or, if you want to leave it in your working tree and have done step 0
3.   git rm --cached a/submodule
3bis mv a/submodule_tmp a/submodule
```