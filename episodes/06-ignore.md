---
title: Ignoring Things
teaching: 5
exercises: 0
---

::::::::::::::::::::::::::::::::::::::: objectives

- Configure Git to ignore specific files.
- Explain why ignoring files can be useful.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- How can I tell Git to ignore files I don't want to track?

::::::::::::::::::::::::::::::::::::::::::::::::::

What if we have files that we do not want Git to track for us,
like backup files created by our editor
or intermediate files created during data analysis?
Let's create a few dummy files:

```bash
$ mkdir data
$ touch a.png b.png c.png data/a.csv data/b.nc
```

and see what Git says:

```bash
$ git status
```

```output
On branch forecast
Untracked files:
  (use "git add <file>..." to include in what will be committed)

	a.png
	b.png
	c.png
	data/

nothing added to commit but untracked files present (use "git add" to track)
```

Putting these files under version control would be a waste of disk space.
What's worse,
having them all listed could distract us from changes that actually matter,
so let's tell Git to ignore them.

We do this by creating a file in the root directory of our project called `.gitignore`:

```bash
$ nano .gitignore
$ cat .gitignore
```

```output
*.png
data/
```

These patterns tell Git to ignore any file whose name ends in `.png`
and everything in the `data` directory.
(If any of these files were already being tracked,
Git would continue to track them.)

Once we have created this file,
the output of `git status` is much cleaner:

```bash
$ git status
```

```output
On branch forecast
Untracked files:
  (use "git add <file>..." to include in what will be committed)

	.gitignore

nothing added to commit but untracked files present (use "git add" to track)
```

The only thing Git notices now is the newly-created `.gitignore` file.
You might think we wouldn't want to track it,
but everyone we're sharing our repository with will probably want to ignore
the same things that we're ignoring.
Let's add and commit `.gitignore`:

```bash
$ git add .gitignore
$ git commit -m "Ignore png files and the data folder"
$ git status
```

```output
On branch forecast
nothing to commit, working tree clean
```

As a bonus, using `.gitignore` helps us avoid accidentally adding files to the repository that we don't want to track:

```bash
$ git add a.png
```

```output
The following paths are ignored by one of your .gitignore files:
a.png
Use -f if you really want to add them.
```

If we really want to override our ignore settings,
we can use `git add -f` to force Git to add something. For example,
`git add -f a.csv`.
We can also always see the status of ignored files if we want:

```bash
$ git status --ignored
```

```output
On branch forecast
Ignored files:
 (use "git add -f <file>..." to include in what will be committed)

        a.png
        b.png
        c.png
        data/

nothing to commit, working tree clean
```

:::::::::::::::::::::::::::::::::::::::  challenge

## Ignoring Nested Files

Given a directory structure that looks like:

```bash
data/csv
data/plots
```

How would you ignore only `data/plots` and not `data/csv`?

:::::::::::::::  solution

## Solution

If you only want to ignore the contents of
`data/plots`, you can change your `.gitignore` to ignore
only the `/plots/` subfolder by adding the following line to
your .gitignore:

```output
data/plots/
```

This line will ensure only the contents of `data/plots` is ignored, and
not the contents of `data/csv`.

As with most programming issues, there
are a few alternative ways that one may ensure this ignore rule is followed.
The "Ignoring Nested Files: Variation" exercise has a slightly
different directory structure
that presents an alternative solution.
Further, the discussion page has more detail on ignore rules.



:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::  challenge

## Including Specific Files

How would you ignore all `.png` files in your root directory except for
`final.png`?
Hint: Find out what `!` (the exclamation point operator) does

:::::::::::::::  solution

## Solution

You would add the following two lines to your .gitignore:

```output
*.png           # ignore all png files
!final.png      # except final.png
```

The exclamation point operator will include a previously excluded entry.

Note if you've previously committed `.png` files 
they will not be ignored with this new rule. Only future additions
of `.png` files added to the root directory will be ignored.



:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::  challenge

## Ignoring Nested Files: Variation

Given a directory structure that looks similar to the earlier Nested Files
exercise, but with a slightly different directory structure:

```bash
data/csv
data/images
data/plots
data/analysis
```

How would you ignore all of the contents in the data folder, but not `data/csv`?

Hint: think a bit about how you created an exception with the `!` operator
before.

:::::::::::::::  solution

## Solution

If you want to ignore the contents of
`data/` but not those of `data/csv/`, you can change your `.gitignore` to ignore
the contents of data folder, but create an exception for the contents of the
`data/csv` subfolder. Your .gitignore would look like this:

```output
data/*               # ignore everything in data folder
!data/csv/          # do not ignore data/csv/ contents
```

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::  challenge

## Ignoring all data Files in a Directory

Assuming you have an empty .gitignore file, and given a directory structure that looks like:

```bash
data/csv/global/temperature/a.dat
data/csv/global/temperature/b.dat
data/csv/global/temperature/c.dat
data/csv/global/temperature/info.txt
data/plots
```

What's the shortest `.gitignore` rule you could write to ignore all `.dat`
files in `data/csv/global/temperature`? Do not ignore the `info.txt`.

:::::::::::::::  solution

## Solution

Appending `data/csv/global/temperature/*.dat` will match every file in `data/csv/global/temperature`
that ends with `.dat`.
The file `data/csv/global/temperature/info.txt` will not be ignored.



:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::  challenge

## Ignoring all data Files in the repository

Let us assume you have many `.csv` files in different subdirectories of your repository.
For example, you might have:

```bash
results/a.csv
data/experiment_1/b.csv
data/experiment_2/c.csv
data/experiment_2/variation_1/d.csv
```

How do you ignore all the `.csv` files, without explicitly listing the names of the corresponding folders?

:::::::::::::::  solution

## Solution

In the `.gitignore` file, write:

```output
**/*.csv
```

This will ignore all the `.csv` files, regardless of their position in the directory tree.
You can still include some specific exception with the exclamation point operator.



:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::  challenge

## The Order of Rules

Given a `.gitignore` file with the following contents:

```bash
*.csv
!*.csv
```

What will be the result?

:::::::::::::::  solution

## Solution

The `!` modifier will negate an entry from a previously defined ignore pattern.
Because the `!*.csv` entry negates all of the previous `.csv` files in the `.gitignore`,
none of them will be ignored, and all `.csv` files will be tracked.

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::  challenge

## Log Files

You wrote a script that creates many intermediate log-files of the form `log_01`, `log_02`, `log_03`, etc.
You want to keep them but you do not want to track them through `git`.

1. Write **one** `.gitignore` entry that excludes files of the form `log_01`, `log_02`, etc.

2. Test your "ignore pattern" by creating some dummy files of the form `log_01`, etc.

3. You find that the file `log_01` is very important after all, add it to the tracked files without changing the `.gitignore` again.

4. Discuss with your neighbor what other types of files could reside in your directory that you do not want to track and thus would exclude via `.gitignore`.

:::::::::::::::  solution

## Solution

1. append either `log_*`  or  `log*`  as a new entry in your .gitignore
2. track `log_01` using   `git add -f log_01`
  
  

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: keypoints

- The `.gitignore` file tells Git what files to ignore.

::::::::::::::::::::::::::::::::::::::::::::::::::


