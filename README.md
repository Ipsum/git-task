# git-task
Attach local notes/tasks to your git branches

# Usage
```
Usage:
  git-task add "some task I should do"
  git-task list
  git-task rm <index of entry to delete from list>
```

```
[tyler ~/some-git-repo]$ git checkout -b my_new_branch
[tyler ~/some-git-repo]$ git task add "Write some tests!"
[tyler ~/some-git-repo]$ git task add "Write some docs!"
[tyler ~/some-git-repo]$ git task list
* my_new_branch *
     1	Write some tests!
     2	Write some docs!
[tyler ~/some-git-repo]$ git task rm 1
[tyler ~/some-git-repo]$ git task list
* my_new_branch *
     1	Write some docs!
[tyler ~/some-git-repo]$ git checkout -b some_other_branch
[tyler ~/some-git-repo]$ git task list
No tasks yet
[tyler ~/some-git-repo]$ git checkout my_new_branch
[tyler ~/some-git-repo]$ git task list
* my_new_branch *
     1	Write some docs!
```

# Setup
* Add git-task to your PATH
* Setup GIT_TASK_DIR with the local path to save your tasks to.
```
export GIT_TASK_DIR=~/git-task-files
```
* Add the following to your `~/.gitconfig`:
```
[alias]
        task = !bash -c \"git-task $@\"
```
* Optionally export the callback `git_task_filter` function. This will allow you to configure how the task files are named.

# git_task_filter
This bash function will allow you to apply your own filter to create the name of the task list. The default behavior is to name the task list:
```
repo-folder-name+my_branch_name
```
but by defining your own filter you can make it anything you want.

Typically this function is defined and exported in your `.bashrc`.

## Prototype
```
task_list_name=$(git_task_filter $branch_name $absolute_path_to_git_repo)
```

## Examples
### Extracting a Jira Ticket
In .bashrc:
```
function git_task_filter() {
branch_name=$1
dir_path=$2
grep -Po "\/\K[A-Z]*-[0-9]*" <<< "$branch_name" || echo ""
}
export -f git_task_filter
```

If your branches are named as `feature/JIRA-123-some-branch-name`, this filter will extract `JIRA-123` and name your task list after that. If you have multiple repos that reference this same ticket, they will share the same task list.
