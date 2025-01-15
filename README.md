# git-gpt-commit

Git command to make a commit message draft by ChatGPT

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
In addition, [-c | --change] option is available to tell contents of changes to git-gpt-commit.

You can change PROMPT, EXCLUDE, and MESSAGE by editing ${XDG_CONFIG_HOME:-$HOME/.config}/git_gpt_commit/config.
PROMPT is a prompt message how to make a commit message from git diff.
EXCLUDE is a regex to exclude files from git diff. Multiple regexes can be separated by ','.
TEMPLATE will be shown under the commit message as a comment.

Default values are:
    # ChatGPT model. If empty, cg's default value is used.
    MODEL=""

    # Prompt to make commit messages from git diff.
    PROMPT="You will be provided with git diff output. Based on the provided diff, create concise and clear git commit messages.

Each commit message must:
- Be written on one line.
- Start with one of the following prefixes, depending on the type of change:
  - feat: (introducing a new feature or functionality visible to the user)
  - fix: (fixing a bug or issue that affects users)
  - docs: (updating or improving documentation only)
  - style: (non-functional changes like code formatting, removing extra spaces, etc.)
  - refactor: (modifying existing code without changing its behavior, e.g., renaming variables, restructuring code)
  - test: (adding or updating tests without changing production code)
  - chore: (updates to build tools, configurations, or non-production-related changes)

Rules for output:
1. Only output plain commit messages with no additional comments, bullet points, or formatting.
2. Summarize changes into as few lines as possible, ideally within 3 lines.
3. Group related changes under the same category when possible.
4. Separate categories only if they address distinct types of changes.

Example output format:
feat: add get_name function to get user name
test: add test for get_name

"

    # Regex to exclude files from git diff. Multiple regexes can be separated by ','.
    EXCLUDE="*.lock"

    # Commit message template. If empty, use content of $(git config --get commit.template) if exists.
    # If not defined, use content of $(git config --get commit.template) if exists. Set 'MESSAGE=""' to drop the message.
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

## Example

When `git diff --cached` shows like this (`git diff 8bdb538790b20a785acca39b13ef676676a3ebdc 3218ca3d7d41641727c364e65b055c068293f703` of this repository):

```
diff --git a/bin/git-gpt-commit b/bin/git-gpt-commit
index 299b5c5..a7aac69 100755
--- a/bin/git-gpt-commit
+++ b/bin/git-gpt-commit
@@ -32,6 +32,8 @@ if [ "$1" = "-h" ] || [ "$1" = "--help" ]; then
   echo "Usage: git gpt-commit [options] [--] [<pathspec>...]
 
 Options are the same as git commit.
+In addition, [-c | --change] option is available to tell contents of changes to $0.
+
 You can change PROMPT, EXCLUDE, and MESSAGE by editing \${XDG_CONFIG_HOME:-\$HOME/.config}/git_gpt_commit/config.
 PROMPT is a prompt message how to make a commit message from git diff.
 EXCLUDE is a regex to exclude files from git diff. Multiple regexes can be separated by ','.
@@ -77,6 +79,24 @@ if echo " $* " | grep -q " -a ";then
   git add -u
 fi
 
+change=""
+if echo " $* " | grep -q -e " -c " -e " --change ";then
+  is_change=0
+  for v;do
+    shift
+    if [ "$v" = "-c" ] || [ "$v" = "--change" ];then
+      is_change=1
+      continue
+    fi
+    if [ "$is_change" -eq 1 ];then
+      change="This commit is for: $v"$'\n'
+      is_change=0
+      continue
+    fi
+    set -- "$@" "$v"
+  done
+fi
+
 function restore {
   if [ -n "$modified" ];then
     for f in $modified;do
@@ -118,7 +138,7 @@ if [ -n "$EXCLUDE" ];then
   exclude_list="-- $exclude_list"
 fi
 
-commit_message="$(cg ask $MODEL "$PROMPT
+commit_message="$(cg ask $MODEL "${change}$PROMPT
 
 ### diff
```

`git gpt-commmit` makes a draft like:

```
refactor: Add option to include change descriptions in commit messages
```

If you specify changes by `-c`/`--change`, it will make more sensible draft.

```
$ git gpt-commit -c "add -c/--change options"
```

This makes

```
feat: Add -c/--change option to specify contents of changes in commit message
```





 
