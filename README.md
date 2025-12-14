# git-stock

A git extension that allows you to "stock" your uncommitted changes to a remote Git repository.

Makes it easy to transfer your work-in-progress between different machines or share it with others without creating temporary branches.

Think of it as a shared, multi-machine `git stash`.

## Features

- **Remote Storage**: Stocks are pushed to a central repository, accessible from any machine.
- **Project-Scoped**: Stocks are automatically namespaced by the project's origin URL, so you can use the same stock repository for all your projects.
- **Familiar Commands**: Implements a `git stash`-like interface (`push`, `pop`, `apply`, `list`, etc.).
- **Efficient**: Uses a local cache for fast operations.
- **Flexible**: Create branches from stocks, amend stock messages, and more.

## Installation

1. **Download the script**
    ```bash
    curl -fsSLO https://raw.githubusercontent.com/Euxcbsks/git-stock/main/git-stock
    ```

2. **Make it executable**
    ```bash
    chmod +x git-stock
    ```

3. **Place the script in your PATH**

    A common location is `/usr/local/bin` or `~/.local/bin` directory that you've added to your `PATH`.

    ```bash
    cp git-stock /usr/local/bin/
    ```

As long as `git-stock` is in your `PATH`, `git` will recognize it as an extension. You can then run it as `git stock <command>`.

## Configuration

Before you can use `git-stock`, you must have a remote, **bare** repository where you want to store your stocks.

You can create a new dedicated GitHub repository or use an existing one.

Then set the `stock.repo` option to your bare repository url.

```bash
git config set --global stock.repo <your-bare-repo-url>
```

## Usage

### `push [<message>]`

Stashes the current changes and pushes them as a new stock to the remote repository.

```bash
# Push with an auto-generated message
git stock push

# Push with a custom message
git stock push "My work on the new feature"
```

Same as `git stash`, this command won't run `git add` automatically by default.

And if there are no local changes, the command will do nothing.

Also, this is the default command, so you can use `git stock` to stock your changes, this is same as `git stock push`.

And custom message also works with `git stock "Another work on the new feature"`.

### `list`

Lists all available stocks for the current project from the remote repository.

```bash
git stock list
# Output:
# 4     2 hours ago    WIP on feature/auth: 35f2d79 Add login form
# 3     8 hours ago    Fixing the navbar layout
# 2     yesterday      (No Message)
# ...
```

### `apply [<id>]`

Applies a stock to the current working directory, leaving the stock on the remote.

```bash
# Apply the latest stock
git stock apply

# Apply a specific stock by ID
git stock apply 3
```

### `pop [<id>]`

Applies a stock to the current working directory and **removes** it from the remote repository upon success.

```bash
# Pop the latest stock
git stock pop

# Pop a specific stock by ID
git stock pop 3
```

### `fetch [<id>|all]`

Fetches remote stock and saves it to your local `git stash list`.

```bash
# Fetch the latest stock into local stash list
git stock fetch

# Fetch a specific stock by ID
git stock fetch 2

# Pull all stocks
git stock fetch all
```

### `drop [<id>]`

Deletes a specific stock from the remote repository.

```bash
# Drop the latest stock
git stock drop

# Drop a specific stock by ID
git stock drop 2
```

### `show [<id>] [-p | --patch]`

Shows the summary or patch of a stock.

```bash
# Show summary of the latest stock
git stock show

# Show summary of a specific stock
git stock show 3

# Show the full patch of a specific stock
git stock show 3 --patch
# or
git stock show -p 3
```

### `diff [<id>]`

Shows the diff between the current `HEAD` and a stock.

```bash
# Diff against the latest stock
git stock diff

# Diff against a specific stock
git stock diff 2
```

### `branch <new_branch_name> [<id>]`

Creates a new branch from a stock and then applies the stashed changes. This is useful for turning a work-in-progress into a formal branch.

```bash
# Create a branch 'feature/new-login' from the latest stock
git stock branch feature/new-login

# Create a branch from a specific stock
git stock branch feature/new-login 2
```

### `amend [<id>] <message>`

Amends the message of a stock.

```bash
# Amend the latest stock's message
git stock amend "A more descriptive message"

# Amend a specific stock's message
git stock amend 3 "Another descriptive message"
```

### `clear`

Removes **all** stocks for the current project from the remote repository. Won't confirm, use with caution.

```bash
git stock clear
```

### `cache`

Manages the local cache.

```bash
# Prune and garbage-collect the local cache repository
git stock cache prune
```
