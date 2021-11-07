# git-reject-binaries-and-large-files

A git pre-receive hook to reject binaries and large files. Also, a pre-commit hook for rejecting merge markers.

## pre-receive hook

This hook prevents users from committing binary files and files greater than 1MB. This can be easily altered, or either restriction can be disabled. To override the restrictions, add 'override restrictions' to the commit message corresponding to the problematic files.

This hook must be installed server-side in the hooks subdirectory. It is only triggered when a user pushes to the server. Committing locally will not be affected. This means that if a commit is made with a binary, the user won't notice the problem until they try to push to the server. To fix the problem, the user will have to make judicious use of git rebase -i to remove the commit, edit it to remove the problematic files, or edit the message to add the override text.

For use with gitolite, the hook must be installed in the repo-specific local code directory known to the git user and the gitolite system. See here:  
http://gitolite.com/gitolite/non-core.html#rsh

The pre-receive hook, which was originally called 'check-binary', is based off of two other pre-receive hooks found on github:  
https://github.com/avar/pre-receive-reject-binaries  
https://github.com/amacneil/git-banish-large-files

However, because pre-receive hooks cannot be chained (you can only have one at a time), I wrote my own to combine the features I wanted from both of the above hooks.

## pre-commit hook

I've also included a bonus pre-commit hook for preventing merge markers (`<<<<<<<`, `>>>>>>>`, `=======`) from being left in a commit. It's careful enough to only check for those symbols at the start of a line, with the exact count, followed by a space or newline. This could also be included in the pre-receive hook as well, but I haven't had a need to do that yet.

### Installation with [pre-commit](https://pre-commit.com/)

Copy the pre-commit script in this repo to a file called `reject_merge_markers.sh` and add it to the project's `.pre-commit-confic.yaml`:

```
  - id: reject_merge_markers
    name: "Reject merge markers"
    language: "script"
    entry: tools/reject_merge_markers.sh
```
