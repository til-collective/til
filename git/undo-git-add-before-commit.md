You can undo git add before commit with

```git
git reset <file>
```

which will remove it from the current index (the "about to be committed" list) without changing anything else.

You can use

```git
git reset
```

without any file name to unstage all due changes. This can come in handy when there are too many files to be listed one by one in a reasonable amount of time.

In old versions of Git, the above commands are equivalent to `git reset HEAD <file>` and `git reset HEAD` respectively, and will fail if `HEAD` is undefined (because you haven't yet made any commits in your repo) or ambiguous (because you created a branch called `HEAD`, which is a stupid thing that you shouldn't do). This was changed in Git 1.8.2, though, so in modern versions of Git you can use the commands above even prior to making your first commit:

>
"git reset" (without options or parameters) used to error out when you do not have any commits in your history, but it now gives you an empty index (to match non-existent commit you are not even on).
