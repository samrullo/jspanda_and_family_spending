# Correct way of checking out remote branch

To correctly check out the remote mdevelop branch to your local repository, you should use:

```bash
git checkout -b mdevelop origin/mdevelop
```

This command does two things:
	•	Creates a new local branch named mdevelop.
	•	Sets it to track the remote branch origin/mdevelop.

After this command, your current branch will be mdevelop, and it will be synchronized with the remote branch.

If you already have a local branch named mdevelop, you can switch to it directly using:

git checkout mdevelop

Then update it with the remote content:

git pull origin mdevelop