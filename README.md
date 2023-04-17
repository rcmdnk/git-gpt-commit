# git-gpt-commit

Git command to make a commit message by ChatGPT

## Installation

By Homebrew:

```
$ brew install rcmdnk/rcmdnkpac/git-gpt-commit
```

or put the script `bin/git-gpt-commit` into the directory under the `PATH`.

## Usage

```
Usage: git gpt-commit [options] [--] [<pathspec>...]

Options are the same as git commit.
You can change PROMPT and MESSAGE by editing $XDG_CONFIG_HOME}/.config/git_gpt_commit/config.
PROMPT is a prompt message how to make a commit message from git diff.
TEMPLATE will be shown under the commit message as a comment.

Default values are:

PROMPT="Please make git commit messages for the following diff output.

Each commit message must be one line starting with one of the following words.

* feat: (new feature for the user, not a new feature for build script)
* fix: (bug fix for the user, not a fix to a build script)
* docs: (changes to the documentation)
* style: (formatting, missing semi colons, etc; no production code change)
* refactor: (refactoring production code, eg. renaming a variable)
* test: (adding missing tests, refactoring tests; no production code change)
* chore: (updating grunt tasks etc; no production code change)
"

MESSAGE="" # Empty. Use content of $(git config --get commit.message)
```

## Notes

### alias

You may want to shorter command.

Add alias like:

```
$ git config --global --add alias.c gpt-commit
```

Then, you can use `git c` as `git gpt-commit`.

### Cancel a commit

To cancel a commit, just remove all messages and exit a editor as usual unless `--allow-empty-message` is given.

### -a option

For now, when you use `-a` option, modified files will be added and they are not reverted even if you cancel the commit.
