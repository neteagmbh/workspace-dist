# zsh workspaces

*zsh workspaces* is a tool to create and manage development workspaces.

## Setup

This tool is written in rust with additional support in zsh / bash.

### Dependencies

* glow - cli command for rendering markdown files
* git - repository management


### Compilation Instructions

Build the release binary using Cargo:

```bash
cargo build --release
```

Or use the provided Makefile:

```bash
make build
```

The compiled binary will be located at `target/release/workspace`.

### Installation Instructions

To install the compiled binary to `/usr/local/bin`, use the Makefile:

```bash
sudo make install
```

This will:
1. Build the release binary if not already built
2. Install the `workspace` binary to `/usr/local/bin` with proper permissions (755)

### Shell Alias

For convenience, you can create an alias `ws` for `workspace activate`.

**Zsh/Bash:**
```bash
alias ws='workspace activate'
```
Or use the tool to generate it:
```bash
eval "$(workspace alias --shell zsh)"
```

**PowerShell:**
```powershell
function ws { workspace activate @args }
```
Or use the tool to generate it:
```powershell
workspace alias --shell powershell | Out-String | Invoke-Expression
```

To make the alias permanent, add the corresponding line to your shell profile (`~/.zshrc`, `~/.bashrc`, or your PowerShell profile).

To uninstall:

```bash
sudo make uninstall
```

**Manual Installation:**

If you prefer to install manually:

```bash
cargo build --release
sudo install -m 755 target/release/workspace /usr/local/bin/workspace
```

## Usage

### Quick Start

List available workspaces:
```bash
workspace list
```

Activate a workspace:
```bash
workspace activate myproject
```

Activate a project:
```bash
workspace activate -p myproject
```

Create a new workspace (with YYYYMMDD prefix):
```bash
workspace create "My New Workspace"
```

Create a new project (without date prefix):
```bash
workspace create -p "my-api-service"
```

Create in a specific repository:
```bash
workspace create -r myrepo "My Workspace"
```

Create in a subdirectory (for repos with subdirs):
```bash
workspace create -p -r myrepo -s backend "auth-service"
```

Create by cloning from Git:
```bash
# Projects - no date prefix
workspace create -p -g "https://github.com/user/repo.git" "my-project"

# Workspaces - adds YYYYMMDD prefix
workspace create -g "https://github.com/user/repo.git" "MyWorkspace"
# Creates: 20260315 MyWorkspace
```

Clone with name inference (name extracted from URL):
```bash
workspace create -p -g "https://github.com/user/my-service.git"
# Creates: my-service
```

Clone with relative URL (using repository's git_url_prefix):
```bash
# First, configure a repository with a prefix
workspace config add-repository github /path/to/repos --git-url-prefix "https://github.com/myorg/"

# Then use relative URLs
workspace create -p -r github -g "my-repo.git"
# Clones from: https://github.com/myorg/my-repo.git
```

Clone and checkout a specific branch:
```bash
workspace create -p -g "https://github.com/user/repo.git" -b develop "my-project"
```

### Cloning Existing Workspaces

The `clone` command creates a new workspace by copying an existing one. It uses efficient CoW (Copy-on-Write) reflink copying where supported by the filesystem (e.g., APFS on macOS, Btrfs/XFS on Linux).

Clone a workspace by name/pattern:
```bash
workspace clone "Source Workspace" "New Workspace Name"
```

Clone a project:
```bash
workspace clone -p "my-service" "my-service-copy"
```

Clone from a specific repository and specify target repository:
```bash
workspace clone myrepo:source-ws "new-ws" --repository target-repo
```

### Pattern Matching and Wildcards

The tool supports powerful pattern matching for workspace and project selection. For detailed help on pattern syntax and wildcards:

```bash
workspace patterns
```

For extended examples:
```bash
workspace patterns --examples
```

**Quick Pattern Reference:**

```
Pattern Syntax: [repository[/subdirectory]:]workspace

Wildcards:
  *      - Match any number of characters
  ?      - Match exactly one character
  [abc]  - Match one character from set
  [a-z]  - Match one character from range

Examples:
  workspace activate myapp           # Single repo: activate 'myapp'
  workspace activate :myapp          # Any repo: activate 'myapp'
  workspace list :test*              # List all starting with 'test'
  workspace activate -p api*         # Activate project starting with 'api'
  workspace list myrepo/*:*api*      # List workspaces with 'api' in any subdir
```

### Configuration

View configuration:
```bash
workspace config show
```

Add a repository:
```bash
workspace config add-repository myrepo /path/to/repo
```

For more configuration options, see the [Configuration Management](doc/configuration_management.md) documentation.

## Author contact

Contact: Michael Fromme, netea GmbH, Hannover, Germany, fromme@netea.de

License: MIT License

