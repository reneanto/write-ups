# Github
##  Git authentication via CLI

```text
git clone repo

git remote remove origin

git remote add origin https://git-token@github.com/owner/repo

git push --set-upstream origin main

cd <dir/report.md>

git add .

git commit -m 'report.md'

```

## error-1

```bash
#if the following error happens during git commit

#On branch main
#Your branch and 'origin/main' have diverged,
#and have 2 and 1 different commits each, respectively.

#then merge the origin and main with the following command

git merge origin/main

```
