# git-stock

A Git extension that allows you to "stock" uncommitted changes to a **separate** remote repository.

It simplifies transferring work-in-progress between machines without creating temporary branches. By offloading changes to a dedicated repository, it ensures your current project remains unpolluted.

Think of it as a shared, multi-machine `git stash`.

## Table of Contents

- [Features](#features)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
  - [push](#push-message)
  - [list](#list)
  - [apply](#apply-id)
  - [pop](#pop-id)
  - [fetch](#fetch-idall)
  - [drop](#drop-id)
  - [show](#show-id--p----patch)
  - [diff](#diff-id)
  - [branch](#branch-new_branch_name-id)
  - [amend](#amend-id-message)
  - [clear](#clear)
  - [cache](#cache)
- [Disclaimer](#disclaimer)

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

A remote **bare** repository is required to use `git-stock`. You may create a new dedicated GitHub repository or use an existing one.

To configure, set the `stock.repo` option to your repository URL:

```bash
git config set --global stock.repo <your-bare-repo-url>
```

> **Note**: Only SSH URLs are supported.

## Usage

### `push [<message>]`

Stashes local changes and uploads them to the remote repository.

```bash
# Push with an auto-generated message
git stock push

# Push with a custom message
git stock push "My work on the new feature"
```

- Default Alias: push is the default action. Running git stock or git stock "message" is equivalent to the commands above.

- Staging: Like git stash, untracked files are excluded unless explicitly staged.

- Empty State: The command performs no action if there are no local changes.

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

Applies a stock to the working directory while **keeping** it in the remote repository.

```bash
# Apply the latest stock
git stock apply

# Apply a specific stock by ID
git stock apply 3
```

### `pop [<id>]`

Applies a stock to the working directory and removes it from the remote repository.

```bash
# Pop the latest stock
git stock pop

# Pop a specific stock by ID
git stock pop 3
```

### `fetch [<id>|all]`

Downloads remote stock(s) into the local git stash list.

```bash
# Fetch the latest stock into local stash list
git stock fetch

# Fetch a specific stock by ID
git stock fetch 2

# Fetch all stocks
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

Displays the summary or full patch diff of a stock.

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

Creates and checks out a new branch, then applies the specified stock. Useful for promoting WIP changes to a formal feature branch.

```bash
# Create a branch 'feature/new-login' from the latest stock
git stock branch feature/new-login

# Create a branch from a specific stock
git stock branch feature/new-login 2
```

### `amend [<id>] <message>`

Updates the message of an existing stock.

```bash
# Amend the latest stock's message
git stock amend "A more descriptive message"

# Amend a specific stock's message
git stock amend 3 "Another descriptive message"
```

### `clear`

Removes **all** stocks for the current project from the remote repository.

> **Warning**: This action is immediate and requires no confirmation.

```bash
git stock clear
```

### `cache`

Manages the internal local cache repository.

```bash
# Prune and garbage-collect the local cache repository
git stock cache prune
```

## Disclaimer

**No Warranty**: `git-stock` is provided "as is," without warranty of any kind, express or implied. The authors and contributors are not liable for any damages or data loss arising from the use of this software.

**Usage Warnings**:
* **Data Loss**: Commands like `git stock clear` are destructive and immediate. Always ensure you have backups or have pushed critical commits before performing bulk operations.
* **Remote Security**: This tool uploads uncommitted code to the repository defined in `stock.repo`. It is the user's responsibility to ensure this remote repository is private and secure. Using a public repository configuration will expose your work-in-progress to the public.
* **Experimental Nature**: This is a third-party Git extension. While designed to be safe, edge cases in network connectivity or Git versions may produce unexpected results.
