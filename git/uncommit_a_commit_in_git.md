# How to Uncommit a commit in git

Sometimes it happens that you mistakenly commit the files, now you want to uncommit some files. 

![alt text](images/mistake_commited.png "Commited By Mistake")

To uncommit these files use the following command in terminal/command prompt 

```git
git reset --soft HEAD^

```

![alt text](images/commit_reversed.png "Commit Reversed")

### Resources:
* https://stackoverflow.com/questions/2845731/how-to-uncommit-my-last-commit-in-git