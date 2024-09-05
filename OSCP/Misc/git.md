
show commit history:
```
git log
```

this may reveal some interesting information. such as changes that might have removed internal network access - which we may be able to roll back to a previous commit and instantiate internal access.

show differences between commits:
```
git show <commit hash>
```

This may reveal sensitive information like usernames and passwords as well as give us some insight into what may be available to us within another commit.

we can move to another commit by using checkout with a commit hash:
```
git checkout <commit hash>
```

We may be able to gain extra scripts that could be useful.

