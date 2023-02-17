## Delete a remote tag

```shell
git push --delete origin ${tag-name}
```

## Overwrite GIT committer and author information

In your terminal window enter the following and update the `OLD_EMAIL`, `CORRECT_NAME` and `CORRECT_EMAIL` values.

```shell
git filter-branch -f --env-filter '

OLD_EMAIL="your-old-email@example.com"
CORRECT_NAME="Your Correct Name"
CORRECT_EMAIL="your-new-email@example.com"

if [ "$GIT_COMMITTER_EMAIL" = "$OLD_EMAIL" ]
then
export GIT_COMMITTER_NAME="$CORRECT_NAME"
export GIT_COMMITTER_EMAIL="$CORRECT_EMAIL"
fi

if [ "$GIT_AUTHOR_EMAIL" = "$OLD_EMAIL" ]
then
export GIT_AUTHOR_NAME="$CORRECT_NAME"
export GIT_AUTHOR_EMAIL="$CORRECT_EMAIL"
fi

' --tag-name-filter cat -- --branches --tags
```

Once complete, check your git history and if you're happy run

```shell
git push --force --tags origin 'refs/heads/*'
```

## Move uncommitted changes from current branch to another branch

```shell
git stash
git checkout my-branch
git stash pop
```

## Print a table showing every branch and its age sorted by the most recent commit

```shell
git for-each-ref --sort=-committerdate refs/remotes/origin/ --format='%(HEAD)%(color:red)%(refname:short)|%(color:green)%(committerdate:relative)|%(color:blue)%(authorname)%(color:reset)' --color=always|column -ts'|'
```
