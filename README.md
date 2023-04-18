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
You can change PROMPT, EXCLUDE, and MESSAGE by editing ${XDG_CONFIG_HOME:-$HOME/.config}/git_gpt_commit/config.
PROMPT is a prompt message how to make a commit message from git diff.
EXCLUDE is a regex to exclude files from git diff. Multiple regexes can be separated by ','.
TEMPLATE will be shown under the commit message as a comment.

Default values are:
    # ChatGPT model. If empty, cg's default value is used.
    MODEL=""

    # Prompt to make commit messages from git diff.
    PROMPT="Please make git commit messages for the following diff output.

Each commit message must be one line starting with one of the following words.

* feat: (new feature for the user, not a new feature for build script)
* fix: (bug fix for the user, not a fix to a build script)
* docs: (changes to the documentation)
* style: (formatting, missing semi colons, etc; no production code change)
* refactor: (refactoring production code, eg. renaming a variable)
* test: (adding missing tests, refactoring tests; no production code change)
* chore: (updating grunt tasks etc; no production code change)"

    # Regex to exclude files from git diff. Multiple regexes can be separated by ','.
    EXCLUDE="*.lock"

    # Commit message template. If empty, use content of $(git config --get commit.template) if exists.
    # If not defined, use content of $(git config --get commit.template) if exists. Set  to drop the message.
    MESSAGE # Not defined.

```

The default prompt tries to create
[Semantic Commit Messages](https://gist.github.com/joshbuchea/6f47e86d2510bce28f8e7f42ae84c716).

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
